<template>
  <div class="container">
    <div class="table-container">
      <Table
        :tableColumns="Columns"
        :tableData="tableData"
        :onVisibleModal="onVisibleModal"
      ></Table>

      <div v-if="modalData">
        <DataModal
          :title="modalData.id"
          :selectedData="modalData"
          :closeModal="closeModal"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Table from "./components/Table.vue";
import DataModal from "./components/DataModal.vue";
import { tableData } from "./utils/tableData.js";

export default {
  name: "App",
  components: {
    Table,
    DataModal,
  },
  data() {
    const Columns = [
      {
        key: "id",
        title: "ID",
        isClickable: true,
        isContent: false,
      },
      {
        key: "amount",
        title: "Amount",
        isClickable: false,
        isContent: false,
      },
      {
        key: "billingPeriod",
        title: "Time Period",
        isContent: true,
        isClickable: false,
      },
      {
        key: "creditsUsed",
        title: "Credits Used",
        isContent: true,
        isClickable: false,
      },
      {
        key: "invoicePaymentStatus",
        title: "Status",
        isContent: true,
        isClickable: false,
      },
      {
        key: "recipt",
        title: " Recipt",
        isContent: true,
        isClickable: false,
      },
    ];
    return {
      Columns,
      tableData,
      modalData: null,
    };
  },
  methods: {
    onVisibleModal(selectedData) {
      this.modalData = selectedData;
    },
    closeModal() {
      this.modalData = null;
    },
  },
};
</script>

<style>
body,
html {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  color: #2c3e50;
  height: 100%;
}
.container {
  padding: 48px 128px;
}

.table-container {
  box-shadow: 0 3px 10px rgb(0 0 0 / 0.2);
}

th,
td {
  padding: 8px;
}

tbody {
  display: block;
}

thead,
tbody tr {
  display: table;
  width: 100%;
  table-layout: fixed;
}

.pg-normal {
  color: #000000;
  font-size: 15px;
  cursor: pointer;
  background: #d0b389;
  padding: 2px 4px 2px 4px;
}

.pg-selected {
  color: #fff;
  font-size: 15px;
  background: #000000;
  padding: 2px 4px 2px 4px;
}

table.yui {
  font-family: arial;
  border-collapse: collapse;
  border: solid 3px #7f7f7f;
  font-size: small;
}

table.yui td {
  padding: 5px;
  border-right: solid 1px #7f7f7f;
}

table.yui .even {
  background-color: #eee8ac;
}

table.yui .odd {
  background-color: #f9fad0;
}

table.yui th {
  border: 1px solid #7f7f7f;
  padding: 5px;
  height: auto;
  background: #d0b389;
}

table.yui th a {
  text-decoration: none;
  text-align: center;
  padding-right: 20px;
  font-weight: bold;
  white-space: nowrap;
}

table.yui tfoot td {
  border-top: 1px solid #7f7f7f;
  background-color: #e1ecf9;
}

table.yui thead td {
  vertical-align: middle;
  background-color: #e1ecf9;
  border: none;
}

table.yui thead .tableHeader {
  font-size: larger;
  font-weight: bold;
}

table.yui thead .filter {
  text-align: right;
}

table.yui tfoot {
  background-color: #e1ecf9;
  text-align: center;
}

table.yui .tablesorterPager {
  padding: 10px 0 10px 0;
}

table.yui .tablesorterPager span {
  padding: 0 5px 0 5px;
}

table.yui .tablesorterPager input.prev {
  width: auto;
  margin-right: 10px;
}

table.yui .tablesorterPager input.next {
  width: auto;
  margin-left: 10px;
}

table.yui .pagedisplay {
  font-size: 10pt;
  width: 30px;
  border: 0px;
  background-color: #e1ecf9;
  text-align: center;
  vertical-align: top;
}

.invoice-input {
  width: 100%;
  padding: 8px;
  background-color: #ecf2f9;
  font-weight: 600;
  font-size: 18px;
  border-radius: 5px;
  border: 1px solid rgba(137, 151, 167, 0.2);
  margin-bottom: 16px;
  margin-top: 8px;
}

.invoice-item-form label {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 8px;
}

input[type="radio"],
input[type="checkbox"] {
  visibility: hidden;
  position: absolute;
}

input[type="radio"] + label:before {
  height: 12px;
  width: 12px;
  margin-right: 2px;
  content: " ";
  display: inline-block;
  vertical-align: baseline;
  border: 1px solid rgba(137, 151, 167, 0.2);
  background: #eef1f3;
}

input[type="radio"]:checked + label:before {
  height: 6px;
  width: 6px;
  border: 4px solid #0283ff;
}

input[type="radio"] + label:before {
  border-radius: 50%;
  margin: 0 8px;
}

.radio-label {
  cursor: pointer;
}

.btn-cancel {
  font-size: 16px;
  font-weight: 600;
  border: 1px solid rgba(137, 151, 167, 0.2);
  border-radius: 3px;
  padding: 8px;
  margin-right: 16px;
  cursor: pointer;
}

.btn-update {
  font-size: 16px;
  font-weight: 600;
  color: #fff;
  background-color: #0283ff;
  border: 1px solid #0283ff;
  border-radius: 3px;
  padding: 8px;
  margin-right: -16px;
  cursor: pointer;
}

.payment-status {
  background-color: #eaf0f7;
  width: fit-content;
  border-radius: 15px;
  padding: 4px 8px;
}

.btn-receipt {
  border: none;
  display: flex;
  align-items: center;
  background-color: #fff;
  font-size: 16px;
  font-weight: 600;
  box-shadow: 0 0 2px 2px rgba(137, 151, 167, 0.2);
}
</style>
