<script>
export default {
  data() {
    return {
      leave_data: [],
      demand_data: [],
      price: [],
      number_clients: '',
      advertising: false,
      development: false,
      site_traffic: '',
      brand_requests: '',
      partners: [],
      strategies: {
        integration: false,
        growth: false,
      },
      conditions: {
        leave: false,
        large_client_base: false,
        competition: false,
        popularity: false,
        new_partners: false
      }
    }
  },
  methods: {
    calculate() {
      {
        // Сравниваем базу фактов с базой знаний
        (this.leave_data[0]/this.leave_data[1])*100 <= 80 ? this.conditions.leave = true : this.conditions.leave = false;
        this.number_clients > 3000 ? this.conditions.large_client_base = true : this.conditions.large_client_base = false;
        this.advertising && ((this.demand_data[1]/this.demand_data[0])*100 >= 130) && ((this.price[1]/this.price[0])*100 >= 130) ? this.conditions.competition = true : this.conditions.competition = false;
        this.site_traffic > 1000 && this.brand_requests > 7000 ? this.conditions.popularity = true : this.conditions.popularity = true
        (this.partners[1]/this.partners[0])*100 >= 120 ? this.conditions.new_partners = true : this.conditions.new_partners = false

        // Выбор стратегии
        this.conditions.leave && this.conditions.large_client_base && (this.conditions.development || this.conditions.competition) ? this.strategies.integration = true : this.strategies.integration = false
        this.conditions.popularity && this.conditions.new_partners && (this.conditions.development || this.conditions.large_client_base) ? this.strategies.growth = true : this.strategies.growth = false
      }
    },
    checkKey(evt) {
      const key = evt.key
      const keysAllowed = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9']

      if (!keysAllowed.includes(key)) {
        evt.preventDefault();
      }
    }
  }
}
</script>

<template>
  <form @submit.prevent>
      <table>
        <tr>
          <th></th>
          <th>Текущий месяц</th>
          <th>Предыдущий месяц</th>
        </tr>
        <tr>
          <td>Кол-во конкурентов</td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="leave_data[1]" />
          </td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="leave_data[0]" />
          </td>
        </tr>
        <tr>
          <td>Количество клиентов</td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="number_clients" />
          </td>
        </tr>
        <tr>
          <td>Количество заказов</td>
          <td>        
            <input type="text" @keypress="checkKey($event)" v-model="demand_data[1]" />
          </td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="demand_data[0]" />
          </td>
        </tr>
        <tr>
          <td>Цена товара</td>
          <td>        
            <input type="text" @keypress="checkKey($event)" v-model="price[1]" />
          </td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="price[0]" />
          </td>
        </tr>
        <tr>
          <td>Количество запросов, связанных с брендом</td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="brand_requests" />
          </td>
        </tr>
        <tr>
          <td>Количество партнеров</td>
          <td>        
            <input type="text" @keypress="checkKey($event)" v-model="partners[1]" />
          </td>
          <td>
            <input type="text" @keypress="checkKey($event)" v-model="partners[0]" />
          </td>
        </tr>
        <tr>
          <td>Посещаемость сайта</td>
          <td>        
            <input type="text" @keypress="checkKey($event)" v-model="site_traffic" />
          </td>
        </tr>
      </table>
    <div class="second">
      <div>
        <input type="checkbox" v-model="advertising" id="c1" />
        <label for="c1">Конкурент обширно рекламировал товар по низким ценам</label>
      </div>
      <div>
        <input type="checkbox" v-model="development" id="c2" />
        <label for="c2">В отрасли развиваются информационные технологии</label>
      </div>
    </div>
    <button @click="calculate">Узнать стратегию</button>
  </form>
  <div>
    <div v-if="strategies.integration">Вам подходит стратегия интеграции</div>
    <div v-if="strategies.growth">Вам подходит стратегия концентрированного роста</div>
  </div>
</template>

<style>
form {
  display: grid;
  gap : 9px 20px;
  width: 900px;
  justify-content: center;
  align-items: center;
  grid-template-columns:  2fr 1fr;
}


/* form > div > div {
  text-align: start;
  vertical-align: middle;
} */

.second > div {
  text-align: start;
}

input[type='number'] {
  display: block;
}

form > div > div > span {
  margin: 10px;
  position: relative;
  top: -2px;
}


label {
  user-select: none;
}
</style>
