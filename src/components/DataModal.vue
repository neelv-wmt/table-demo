<template>
  <div class="modal">
    <div>
      <div class="modal-container" style="width: 500px">
        <div class="modal-header">
          <div class="modal-title">{{ selectedData.id }}</div>
        </div>
        <div class="modal-content">
          <div class="invoice-item-form">
            <form>
              <label>Id</label> <br />
              <input
                type="text"
                readOnly
                class="invoice-input"
                id="id"
                name="id"
                :value="selectedData.id"
              />
              <br />
              <label>Amount</label> <br />
              <input
                type="number"
                class="invoice-input"
                id="amount"
                name="amount"
                :defaultValue="selectedData.amount"
              />
              <br />
              <label>Date</label> <br />
              <input
                type="text"
                class="invoice-input"
                id="billingPeriod"
                name="billingPeriod"
                :defaultValue="selectedData.billingPeriod"
              />
              <br />
              <label>Credit Used</label> <br />
              <input
                type="number"
                class="invoice-input"
                id="creditsUser"
                name="creditsUser"
                :defaultValue="selectedData.creditsUsed"
              />
              <br />
              <input
                type="radio"
                id="paid"
                name="invoicePaymentStatus"
                value="Paid"
                :defaultChecked="selectedData.invoicePaymentStatus === 'Paid'"
              />
              <label htmlFor="paid" class="radio-label"> Paid </label>
              <input
                type="radio"
                id="unpaid"
                name="invoicePaymentStatus"
                value="Not Paid"
                :defaultChecked="selectedData.invoicePaymentStatus === 'Unpaid'"
              />
              <label htmlFor="unpaid" class="radio-label"> Not Paid </label>
              <br />
              <br />
              <div style="display: flex; justify-content: right">
                <input
                  type="button"
                  class="btn-cancel"
                  @click="closeModal"
                  value="Cancel"
                />
                <input
                  type="button"
                  class="btn-update"
                  value="Update Details"
                  @click="closeModal"
                />
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DataModal",
  props: {
    selectedData: { type: Object, default: () => {} },
    closeModal: { type: Function, default: null },
  },
};
</script>

<style scoped>
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0, 0, 0);
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-title {
  font-size: 18px;
  font-weight: 600;
}

.modal-container {
  background-color: #fefefe;
  margin: 32px auto;
  border-radius: 5px;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
  box-shadow: 0 8px 8px -4px rgba(239, 242, 244, 0.8);
}

.modal-content {
  padding: 16px 32px 16px 16px;
}

.close {
  color: #aaa;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>
