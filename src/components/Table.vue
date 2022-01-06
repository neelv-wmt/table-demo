<template>
  <div>
    <div class="table-header">
      <h2 class="no-margin">Invoices</h2>
      <div class="header-extre-container">
        <span class="search">
          <input
            type="text"
            class="search-input"
            placeholder="Search"
            @keyup="handlesearch"
          />
        </span>
        <div class="toggle-btn-container">
          <button
            :class="['pagination-btn', isInfinite ? '' : 'active']"
            @click="handleTogglePagination"
          >
            Limit Offset
          </button>
          <button
            :class="['pagination-btn', isInfinite ? 'active' : '']"
            @click="handleTogglePagination"
          >
            Infinite
          </button>
        </div>
      </div>
    </div>
    <table class="table-style scrolldown">
      <thead>
        <tr style="background-color: #ecf2f9; text-align: left">
          <th
            v-for="(column, index) in Columns"
            :id="column.key"
            :key="index"
            class="header-item"
            @click="
              (key) => {
                sortCol(column.key);
                toggleSort();
              }
            "
            :width="index === 3 ? '22%' : 'auto'"
          >
            {{ column.title }}
          </th>
        </tr>
      </thead>
      <tbody
        style="
          overflow-y: scroll;
          height: calc(100vh - 300px);
          max-height: calc(100vh - 300px);
        "
        id="product-table"
        ref="tbodyRef"
        @scroll="handleTableScroll"
      >
        <tr v-for="(itemData, index) in itemData" :key="index">
          <td
            @click="index === 0 && onVisibleModal(itemData)"
            :class="
              itemCell.isClickable ? 'item-col item-clickable' : 'item-col'
            "
            v-for="(itemCell, index) in Columns"
            :key="index"
            :width="index === 3 ? '22%' : 'auto'"
          >
            <div
              v-if="itemCell.isContent"
              v-html="getColEl(itemCell, itemData)"
            />
            <div v-else>{{ itemData[`${itemCell.key}`] }}</div>
          </td>
        </tr>
      </tbody>
    </table>
    <div v-if="!isInfinite" class="pagination-container">
      <div>
        <ul class="pagination-container">
          <li
            v-for="(pageNumber, index) in paginationRange"
            :key="index"
            :class="[
              `pagination-item`,
              `${page === pageNumber ? 'active' : ''}`,
            ]"
            @click="onPageChange(pageNumber)"
          >
            {{ pageNumber }}
          </li>
        </ul>
      </div>
      <div class="goto-container">
        <span class="goto-text">Go to page</span>
        <input
          id="number"
          class="goto-input"
          type="number"
          @input="onGotoChange"
          ref="gotoRef"
          :max="paginationRange.length"
          min="1"
        />
        <button
          @click="() => onPageChange(parseInt(pageInput, 10))"
          class="btn-go"
        >
          Go
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import DownloadIcon from "../assets/download.png";

export default {
  name: "Table",
  props: {
    tableData: { type: Array, default: () => [] },
    tableColumns: { type: Array, default: () => [] },
    onVisibleModal: { type: Function, default: () => {} },
  },
  data() {
    return {
      ascendingSort: true,
      itemData: this.tableData.slice(0, 10),
      Columns: this.tableColumns,
      togglePagination: false,
      isInfinite: false,
      page: 1,
      PageSize: 10,
      paginationRange: this.getPageRange(this.tableData.length, 10),
      pageInput: 1,
    };
  },

  methods: {
    onGotoChange(e) {
      const element = e.target;
      if (element.type === "number" && element.max && element.min) {
        let value = parseInt(element.value);
        element.value = value; // for 000 like input cleanup to 0
        let max = parseInt(element.max);
        let min = parseInt(element.min);
        if (value > max) element.value = element.max;
        if (value < min) element.value = element.min;
      }
      this.pageInput = e.target.value;
    },
    getPageRange(total, perPage) {
      return this.getPagination(total, perPage);
    },
    onPageChange(pageNumber) {
      this.itemData = this.tableData.slice(
        (pageNumber - 1) * this.PageSize,
        pageNumber * this.PageSize
      );
      this.page = pageNumber;
    },
    range(from, to) {
      let length = to - from + 1;
      return Array.from({ length }, (_, index) => index + from);
    },
    getPagination(totalCount, pageSize) {
      const totalPageCount = Math.ceil(totalCount / pageSize);
      let paginationRange;
      if (pageSize >= totalPageCount) {
        paginationRange = this.range(1, totalPageCount);
      }
      return paginationRange;
    },
    handleTogglePagination() {
      this.isInfinite = !this.isInfinite;
      this.page = 1;
      this.itemData = this.tableData.slice(0, this.PageSize);
      this.$refs.tbodyRef.scrollTop = 0;
    },
    handleTableScroll({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (
        this.isInfinite &&
        this.tableData.length >= this.page * this.PageSize
      ) {
        if (scrollTop + clientHeight >= scrollHeight * 0.9) {
          const newRows = this.tableData.slice(
            this.page * this.PageSize,
            (this.page + 1) * this.PageSize
          );
          this.itemData = [...this.itemData, ...newRows];
          this.page += 1;
        }
      }
    },
    getColEl(colData, rowData) {
      if (colData.key === "billingPeriod") {
        const date = rowData.billingPeriod.split("-");
        return `<div>${date[1]}</div>`;
      } else if (colData.key === "creditsUsed") {
        return `<div style="display: flex; align-items: center"><div class="progress-container"><div class="progress-bar" style="width:${
          (rowData.creditsUsed * 100) / rowData.creditsLimit
        }%"></div></div>${rowData.creditsUsed} / ${rowData.creditsLimit}</div>`;
      } else if (colData.key === "invoicePaymentStatus") {
        return `<span class="payment-status">${
          rowData.invoicePaymentStatus === "Unpaid"
            ? "Not Paid"
            : rowData.invoicePaymentStatus
        }</span>`;
      } else if (colData.key === "recipt") {
        return `<button class="btn-receipt"><img style="width:15px;margin-right:6px"  src=${DownloadIcon}/><span>Receipt</span></button>`;
      } else {
        return `<div></div>`;
      }
    },
    handlesearch(e) {
      this.itemData = this.tableData.filter((dataItem) =>
        dataItem.id.includes(e.target.value)
      );

      return null;
    },
    toggleSort() {
      this.ascendingSort = !this.ascendingSort;
    },
    sortCol(key) {
      this.itemData.sort((a, b) => {
        if (this.ascendingSort) {
          if (a[key] < b[key]) return -1;
          if (a[key] > b[key]) return 1;
        } else {
          if (a[key] > b[key]) return -1;
          if (a[key] < b[key]) return 1;
        }
        return 0;
      });
    },
  },
};
</script>

<style>
.progress-container {
  height: 6px;
  background: lightgrey;
  border-radius: 15px;
  width: 64px;
  border-radius: 25px;
  margin-right: 8px;
}
.progress-bar {
  background: #0283ff;
  height: 6px;
  border-radius: 25px;
}
.table-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
}

.header-item {
  color: #8e99a5;
  font-weight: 500;
  font-size: 18px;
}

.header-item:first-of-type {
  cursor: pointer;
}

.pagination-btn {
  border: none;
  font-size: 14px;
  max-height: 44px;
  padding: 6px 8px;
  cursor: pointer;
  border-radius: 15px;
  background-color: transparent;
}

.pagination-btn.active {
  background-color: #0283ff;
}
.no-margin {
  margin: 0;
}

.search {
  position: relative;
}

.search input {
  padding-right: 32px;
  background-color: #ecf2f9;
  color: #8997a7;
  font-weight: 600;
  font-size: 18px;
  border-radius: 5px;
  border: 1px solid rgba(137, 151, 167, 0.2);
}

.search input:focus {
  border: 1px solid rgba(137, 151, 167, 0.2);
}

.search input:focus-visible {
  border: 1px solid rgba(137, 151, 167, 0.2);
  outline: none;
}

.search::after {
  content: "";
  position: absolute;
  right: 8px;
  top: 0;
  bottom: 0;
  width: 20px;
  background: url("../assets/search.svg") center / contain no-repeat;
}

.search-input {
  padding: 8px;
}

.item-clickable {
  cursor: pointer;
}

.item-clickable:hover {
  color: #0283ff;
  text-decoration: underline;
}

.item-col {
  font-size: 18px;
  font-weight: 600;
}

.pagination-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.pagination-item-container {
  display: flex;
  list-style-type: none;
}

.pagination-item {
  padding: 0 12px;
  height: 32px;
  text-align: center;
  margin: auto 4px;
  display: flex;
  box-sizing: border-box;
  align-items: center;
  min-width: 32px;
  cursor: pointer;
}

.active {
  background-color: #0283ff;
  color: #fff;
  font-weight: 500;
}

.goto-container {
  margin-top: 12px;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}

.goto-text {
  color: rgba(137, 151, 167);
}

.goto-input {
  margin: 0 16px;
  padding: 8px;
  background-color: #ecf2f9;
  font-weight: 600;
  font-size: 18px;
  border-radius: 5px;
  border: 1px solid rgba(137, 151, 167, 0.2);
}

.goto-input:focus {
  border: 1px solid rgba(137, 151, 167, 0.2);
}

.goto-input:focus-visible {
  border: 1px solid rgba(137, 151, 167, 0.2);
  outline: none;
}

.btn-go {
  background-color: #0283ff;
  color: #fff;
  border: none;
  font-weight: 500;
  margin-right: 16px;
  border-radius: 3px;
  padding: 12px;
}
.header-extre-container {
  display: flex;
  align-items: center;
}
.toggle-btn-container {
  margin-left: 12px;
  padding: 6px;

  border-radius: 24px;
  -webkit-box-shadow: 0 6px 6px -6px rgba(0, 0, 0, 0.2);
  -moz-box-shadow: 0 6px 6px -6px rgba(0, 0, 0, 0.2);
  box-shadow: 0 6px 6px -6px rgba(0, 0, 0, 0.2);
}
</style>
>
