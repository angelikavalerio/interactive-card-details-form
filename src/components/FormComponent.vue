<!-- eslint-disable vue/valid-v-model -->
<template>
  <div class="container">
    <div class="credit-card">
      <credit-card :card-details="cardDetails" />
    </div>
    <form v-if="!isSubmitted">
      <label>Cardholder Name</label>
      <input
        type="text"
        v-model="cardDetails.cardHolderName"
        placeholder="e.g. Jane Appleseed"
        :class="{ border: cardDetails.cardHolderName !== '' }"
      />
      <label>Card Number</label>
      <input
        type="text"
        v-model.space="cardDetails.cardNumber"
        placeholder="e.g. 1234 5678 9101 0000"
        :class="{ border: cardDetails.cardNumber.length === 19 }"
      />
      <div class="form-group">
        <div class="expiry">
          <label>Exp. Date (MM/YY)</label>
          <div class="expiry__input">
            <select
              name="month"
              v-model="cardDetails.expiryMonth"
              id="month"
              class="date"
              :class="{ border: cardDetails.expiryMonth !== '' }"
            >
              <option value="" disabled selected hidden>MM</option>
              <option :value="month" v-for="month in months" :key="month">
                {{ month }}
              </option>
            </select>
            <select
              name="month"
              v-model="cardDetails.expiryYear"
              class="date"
              :class="{ border: cardDetails.expiryYear !== '' }"
            >
              <option value="" disabled selected hidden>YY</option>
              <option :value="year" v-for="year in years" :key="year">
                {{ year }}
              </option>
            </select>
          </div>
        </div>
        <div class="cvc">
          <label>CVC</label>
          <input
            type="text"
            v-model.cvc="cardDetails.cvc"
            placeholder="e.g. 123"
            :class="{ border: cardDetails.cvc.length === 3 }"
          />
        </div>
      </div>
      <button
        type="button"
        @click="submitForm"
        :disabled="isDisabled"
        :class="{ disabled: isDisabled }"
      >
        Confirm
      </button>
    </form>
    <form v-else>
      <img
        src="../assets/images/icon-complete.svg"
        alt="completed"
        class="img"
      />
      <span class="submitted thank-you">Thank you!</span>
      <span class="submitted">We've added your card details.</span>
      <button type="button" @click="submitForm">Continue</button>
    </form>
  </div>
</template>
<script setup>
import { ref, vModelText, computed, watchEffect } from "vue";
import CreditCard from "./CreditCard.vue";

const isSubmitted = ref(false);
const isDisabled = ref(true);

const months = computed(() => {
  let arr = [];
  for (let i = 1; i <= 12; i++) {
    arr.push(
      i.toLocaleString("en-US", {
        minimumIntegerDigits: 2,
      })
    );
  }
  return arr;
});

const years = computed(() => {
  let arr = [];
  for (let i = 23; i <= 30; i++) {
    arr.push(i);
  }
  return arr;
});

const checkForEmpty = () => {
  Object.keys(cardDetails.value).map((key) => {
    if (cardDetails.value[key] === "") {
      isDisabled.value = true;
    } else if (cardDetails.value["cardNumber"].length !== 19) {
      isDisabled.value = true;
    } else if (cardDetails.value["cvc"].length !== 3) {
      isDisabled.value = true;
    } else {
      isDisabled.value = false;
    }
  });
};

const cardDetails = ref({
  cardHolderName: "",
  cardNumber: "",
  expiryMonth: "",
  expiryYear: "",
  cvc: "",
});

watchEffect(checkForEmpty, cardDetails.value);

vModelText.updated = (el, { value, modifiers }) => {
  if (value && modifiers.space) {
    if (value.match(/^[\d ]+$/)) {
      var v = value.replace(/\s+/g, "").replace(/[^0-9]/gi, "");
      var matches = v.match(/\d{4,16}/g);
      var match = (matches && matches[0]) || "";
      var parts = [];

      for (let i = 0, len = match.length; i < len; i += 4) {
        parts.push(match.substring(i, i + 4));
      }

      if (parts.length) {
        el.value = parts.join(" ");
      }

      cardDetails.value.cardNumber = el.value;
    } else {
      el.value = value.replace(/\s+/g, "").replace(/[^0-9]/gi, "");
      cardDetails.value.cardNumber = el.value;
    }
  }
  if (value && modifiers.cvc) {
    if (value.match(/^\d+$/)) {
      if (value.length > 3) {
        el.value = value.slice(0, 3);
      }
      cardDetails.value.cvc = el.value;
    } else {
      el.value = value.replace(/\s+/g, "").replace(/[^0-9]/gi, "");
      cardDetails.value.cvc = el.value;
    }
  }
};

const resetForm = () => {
  cardDetails.value = {
    cardHolderName: "",
    cardNumber: "",
    expiryMonth: "",
    expiryYear: "",
    cvc: "",
  };
};

const submitForm = () => {
  if (isSubmitted.value === true) resetForm();
  isSubmitted.value = !isSubmitted.value;
};
</script>

<style scoped>
.border {
  border: 1px solid rgb(149, 131, 230) !important;
}

.disabled {
  background-color: rgb(202, 181, 221);
}
</style>
