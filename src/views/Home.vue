<template>
  <div class="home">
    <b-container fluid>
      <b-row>
        <b-col lg="2">
          <b-table
            ref="workersTable"
            striped
            hover
            :items="workers"
            :fields="workerFields"
            bordered
            selectable
            @row-selected="onWorkerSelected"
            :tbody-tr-class="rowWorkerClass"
          >
          </b-table>
        </b-col>
        <b-col lg="6">
          <b-table
            ref="flightsTable"
            striped
            hover
            :items="flights_of_the_worker"
            :fields="flightFields"
            bordered
            selectable
            @row-selected="onFlightSelected"
            :tbody-tr-class="rowFlightClass"
          ></b-table>
        </b-col>
        <b-col lg="4">
          <b-table
            ref="informationTable"
            stacked
            :items="flight_information"
            bordered
          ></b-table>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      request: null,
      workerFields: [
        {
          key: "name",
          label: "Workers",
          sortable: true,
        },
      ],
      workers: [
        {
          id: "",
          name: "",
          status: "",
        },
      ],
      flightFields: [
        {
          key: "flight_number",
          label: "Flight Number",
          sortable: false,
        },
        {
          key: "origin",
          label: "Origin",
          sortable: false,
        },
        {
          key: "origin_date",
          label: "Origin Date",
          sortable: false,
        },
        {
          key: "destination",
          label: "Destination",
          sortable: false,
        },
        {
          key: "destination_date",
          label: "Destination Date",
          sortable: false,
        },
      ],
      flights_of_the_worker: [
        {
          flight_number: "",
          origin: "",
          origin_date: "",
          destination: "",
          destination_date: "",
          status: "",
        },
      ],
      flight_information: [
        {
          plane_number: "",
          duration: "",
          origin_date: "",
          destination_gate: "",
        },
      ],
      worker_list: [],
      flights_list: [],
      worker_id: null,
      flight_number: null,
    };
  },
  mounted: function() {
    this.getAllWorkers();
  },
  methods: {
    cancel() {
      this.request.cancel();
      this.clearOldRequest();
    },
    clearOldRequest() {
      this.request = null;
    },
    getAllWorkers() {
      this.workers = [];
      axios
        .get("https://interview-mock.herokuapp.com/api/workers/")
        .then((response) => {
          this.worker_list = response.data;
          this.worker_list.forEach((worker) => {
            this.workers.push({
              id: worker.id,
              name: worker.name,
              status: worker.status,
            });
          });
          return this.workers;
        })
        .then((workers) => {
          this.worker_id = workers[0].id;
          this.workers[0].status = "active";
          this.getFlightsOfWorker();
          this.refreshedFlightInfo();
        });
    },
    getFlightsOfWorker() {
      if (this.request) {
        this.cancel();
      }
      const axiosSource = axios.CancelToken.source();
      this.request = { cancel: axiosSource.cancel };
      this.flights_of_the_worker = [];
      axios
        .get(
          "https://interview-mock.herokuapp.com/api/workers/" + this.worker_id,
          {
            cancelToken: axiosSource.token,
          }
        )
        .then((response) => {
          this.flights_list = response.data;
          this.flights_list.forEach((flight) => {
            this.flights_of_the_worker.push({
              flight_number: flight.num,
              origin: flight.from,
              origin_date: flight.from_date,
              destination: flight.to,
              destination_date: flight.to_date,
              status: "",
            });
          });
          this.flights_of_the_worker[0].status = "active";
          this.flight_number = this.flights_of_the_worker[0].flight_number;
          this.flight_information = [
            {
              plane_number: this.flights_list[0].plane,
              duration: this.parseTime(this.flights_list[0].duration),
              origin_date: this.flights_list[0].from_date,
              destination_gate: this.flights_list[0].to_gate,
            },
          ];
          this.clearOldRequest();
        })
        .catch((error) => {
          if (axios.isCancel(error)) {
            console.log("Request canceled", error.message);
          }
        });
    },
    getFlightInformation() {
      this.flight_information = [];
      let flight_info = this.flights_list.find(
        (flight) => flight.num === this.flight_number
      );
      this.flight_information[0] = {
        plane_number: flight_info.plane,
        duration: this.parseTime(flight_info.duration),
        origin_date: flight_info.from_date,
        destination_gate: flight_info.to_gate,
      };
    },
    refreshedFlightInfo() {
      this.interval = setInterval(
        function() {
          axios
            .get(
              "https://interview-mock.herokuapp.com/api/workers/" +
                this.worker_id
            )
            .then((response) => {
              let flights = response.data;
              this.flight_information = [];
              let flight_info = flights.find(
                (flight) => flight.num === this.flight_number
              );
              this.flight_information[0] = {
                plane_number: flight_info.plane,
                duration: this.parseTime(flight_info.duration),
                origin_date: flight_info.from_date,
                destination_gate: flight_info.to_gate,
              };
            });
        }.bind(this),
        60000
      );
    },
    onWorkerSelected(items) {
      if (items.length) {
        this.worker_id = items[0].id;
        this.workers.forEach((worker) => {
          worker.status = "";
        });
        items[0].status = "active";
        this.getFlightsOfWorker();
        this.$refs.workersTable.clearSelected();
        this.flight_information = [
          {
            plane_number: "",
            duration: "",
            origin_date: "",
            destination_gate: "",
          },
        ];
      }
    },
    rowWorkerClass(item, type) {
      if (!item || type !== "row") return;
      if (item.status === "active") return "table-success";
    },
    rowFlightClass(item, type) {
      if (!item || type !== "row") return;
      if (item.status === "active") return "table-success";
    },
    onFlightSelected(items) {
      if (items.length) {
        this.flight_number = items[0].flight_number;
        this.getFlightInformation();
        this.flights_of_the_worker.forEach((flight) => {
          flight.status = "";
        });
        items[0].status = "active";
        this.$refs.flightsTable.clearSelected();
      }
    },
    parseTime(time) {
      let hours = Math.floor(time / 60);
      let minutes = time % 60;
      var newTime;
      if (minutes === 0) {
        newTime = hours + "h";
      } else {
        newTime = hours + "h " + minutes + "m";
      }
      return newTime;
    },
  },
};
</script>
