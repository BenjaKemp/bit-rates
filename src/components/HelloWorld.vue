<template>
  <div class=container>
    <div class="flex-grid">
        <div v-for="(rate, index) in rates" :key="rate.last" class="col" >
          <currency :currency="rate" :code="index"></currency>
      </div>
    </div>
      <div class="form_container">
        <form
          @submit="checkForm"
          method="post"
        >
          <p v-if="errors.length">
            <b>Please correct the following error(s):</b>
            <ul>
              <li v-for="error in errors" :key="error">{{ error }}</li>
            </ul>
          </p>
          <p>
            <label for="amount">Amount</label>
            <input
              id="amount"
              v-model="amount"
              type="number"
              name="amount"
              min="0"
            >
          </p>
          <div class="form_element">
            <label for="currency">Base Currency</label>
            <v-select id="currency" :options="keys" placeholder="base currency" @input="setSelected"/>
          </div>
          <p>
            <input
              type="submit"
              value="Submit"
            >
          </p>
        </form>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import vSelect from 'vue-select'
import Currency from './Currency'
export default {
  name: 'HelloWorld',
  components: {
    vSelect,
    Currency
  },
  data: () => {
    return {
      rates: {},
      selected: null,
      errors: [],
      amount: null
    }
  },
  computed:{
    keys () {
      return Object.keys(this.rates)
    }
  },
  methods: {
    async load () {
      try {
        const response = await axios.get('https://blockchain.info/ticker')
        this.rates = response.data
      } catch (e) {
        console.error(e)
      }
    },
    checkForm(e) {
      e.preventDefault();
      this.errors = [];
      if (!this.amount) {
        this.errors.push('please enter amount to be converted');
      } else if (this.amount==0) {
        this.errors.push('please enter an amount greater than zero');
      } else {
        fetch(`https://blockchain.info/tobtc?currency=${this.selected}&value=${this.amount}`)
        .then(async res => {
          if (res.status === 200) {
             let conversion = await res.json();
            alert(`${this.selected} ${this.amount} is worth ${conversion} bitcoins`);
          } else if (res.status === 400) {
            let errorResponse = await res.json();
            this.errors.push(errorResponse.error);
          }
        })
      }
    },
    setSelected(value){
      this.selected = value
    }
  },
  mounted () {
    this.load();
    setInterval(function () {
      this.load();
    }.bind(this), 100000); 
  }
}
</script>

<style lang="scss">
@import "vue-select/src/scss/vue-select.scss";
  .container {
    display: flex;
    flex-direction: column;
  }
  .flex-grid {
    display: flex;
    flex-wrap: wrap;
  }
  .col {
    flex-basis: 33.3%;
  }

  @media (max-width: 1200px) {
    .col {
      flex: 50%;
    }
  }
  @media (max-width: 800px) {
    .col {
      flex: 100%;
    }
    .form_container {
      width:100%!important
    }
  }
  .form_container {
    width: 50%;
    border: black solid;
    padding: 10px;
    align-self: center;
    .form_element {
      display: flex;
      flex-direction: column;
      padding: 10px;
    }
  }
</style>
