<template>
  <div class="card">
    <div class="card__front">
      <div class="circle">
        <div class="circle-full"></div>
        <div class="circle-plain"></div>
      </div>
      <div class="card__front-text">
        <h2>
          {{
            cardDetails.cardNumber === ""
              ? "0000 0000 0000 0000"
              : cardDetails.cardNumber
          }}
        </h2>
        <div class="card__front-text-below">
          <span class="card-holder">{{
            cardDetails.cardHolderName === ""
              ? "Jane Appleseed"
              : cardDetails.cardHolderName
          }}</span>
          <span>{{
            cardDetails.expiryMonth === "" && cardDetails.expiryYear === ""
              ? "09/23"
              : cardDetails.expiryMonth + "/" + cardDetails.expiryYear
          }}</span>
        </div>
      </div>
    </div>
    <div class="card__back">
      <span>{{ cardDetails.cvc }}</span>
    </div>
  </div>
</template>
<script setup>
import { defineProps } from "vue";

// eslint-disable-next-line no-unused-vars
const props = defineProps(["card-details"]);
</script>
