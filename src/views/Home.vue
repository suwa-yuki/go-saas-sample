<template>
  <div id="form-container">
    <form id="payment-form" @submit="onSubmit">
      <div class="form-row">
        <label for="card-element">
          Credit or debit card
        </label>
        <div id="card-element">
        </div>
        <div id="card-errors" role="alert"></div>
      </div>
      <button>Submit Payment</button>
    </form>
  </div>
</template>

<script>
var stripe = Stripe('pk_test_rFsjJGR2vtb4TkdXxxXAxJvC00j6mIlViQ');

export default {
  name: "home",
  data() {
    return {
      card: {}
    }
  },
  mounted() {
    const elements = stripe.elements();
    const style = {
      base: {
        color: '#32325d',
        fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
        fontSmoothing: 'antialiased',
        fontSize: '16px',
        '::placeholder': {
          color: '#aab7c4'
        }
      },
      invalid: {
        color: '#fa755a',
        iconColor: '#fa755a'
      }
    };
    this.card = elements.create('card', {style: style});
    this.card.mount('#card-element');
    this.card.addEventListener('change', function(event) {
      const displayError = document.getElementById('card-errors');
      if (event.error) {
        displayError.textContent = event.error.message;
      } else {
        displayError.textContent = '';
      }
    });
  },
  methods: {
    async onSubmit(event) {
      event.preventDefault();
      const result = await stripe.createToken(this.card);
      if (result.error) {
        const errorElement = document.getElementById('card-errors');
        errorElement.textContent = result.error.message;
      } else {
        alert(`token : ${result.token.id}`);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
#payment-form {
  margin: 2em;
  width: 300px;
}
#card-element {
  margin: 1em 0 1em 0;
  width: 300px;
}
</style>
