<script>
export default {
  data() {
    return {
      leave_data: [],
      demand_data: [],
      price: [],
      number_clients: "",
      advertising: false,
      development: false,
      site_traffic: "",
      result_strategy: "",
      brand_requests: "",
      partners: [],
      results: [],
      reg_sell: false,
      reg_appoint: false,
      low_temp: false,
      mark: [],
      find_strat: false,
      postavschic_problems: false,
      strategies: {
        integration: {
          value: false,
          name: "уход с рынка конкурентов И большая клиентская база И развитие информационных технологий в отрасли ИЛИ ценовая конкуренция, значит интеграция = истина.",
          name2: "Вам подходит стратегия интеграции",
          rule: () =>
            this.conditions.leave.value &&
            this.conditions.large_client_base.value &&
            (this.development || this.conditions.competition.value),
        },
        growth: {
          value: false,
          name: "известное на рынке имя И появление новых партнеров И развитие информационных технологий в отрасли ИЛИ большая клиентская база, значит концентрированный рост = истина.",
          name2: "Вам подходит стратегия концентрированного роста",
          rule: () =>
            this.conditions.popularity.value &&
            this.conditions.new_partners.value &&
            (this.development || this.conditions.large_client_base.value),
        },
        dever: {
          value: false,
          name: "Если (большая клиентская база = истина ИЛИ ценовая конкуренция = истина) И (низкий спрос на товар ИЛИ низкий темп роста экономики), значит диверсификация = истина.",
          name2: "Вам подходит стратегия диверсификации",
          rule: () =>
            (this.conditions.large_client_base.value ||
              this.conditions.competition.value) &&
            (this.conditions.low_demand.value || this.low_temp),
        },
        reduc: {
          value: false,
          name: "Если низкий спрос на товар И низкий темп роста экономики) И проблемы с поиском новых поставщиков И ценовая конкуренция ИЛИ слабые техники продаж ИЛИ отсутствие бюджета на маркетинг), значит сокращение = истина.",
          name2: "Вам подходит стратегия сокращения",
          rule: () =>
            this.conditions.low_demand.value &&
            this.low_temp.value &&
            this.postavschic_problems &&
            (this.conditions.competition.value ||
              this.conditions.weak_technique.value ||
              this.conditions.marketing_no_budget.value),
        },
      },
      conditions: {
        leave: {
          value: false,
          name: "(количество конкурентов предыдущего месяца меньше текущего количества конкурентов на 20%, значит уход с рынка конкурентов = истина",
          rule: () => (this.leave_data[0] / this.leave_data[1]) * 100 <= 80,
        },
        large_client_base: {
          value: false,
          name: "количество клиентов больше 3000, значит большая клиентская база = истина",
          rule: () => this.number_clients > 3000,
        },
        competition: {
          value: false,
          name: "конкурент обширно рекламировал товар по низким ценам И спрос повысился на 30% И цена товара выросла на 30%, значит ценовая конкуренция = истина",
          rule: () =>
            this.advertising &&
            (this.demand_data[1] / this.demand_data[0]) * 100 >= 130 &&
            (this.price[1] / this.price[0]) * 100 >= 130,
        },
        popularity: {
          value: false,
          name: "посещаемость сайта в сутки > 1000 человек И запросы в интернете связанные с брендом в месяц > 7000, значит известное на рынке имя = истина",
          rule: () => this.site_traffic > 1000 && this.brand_requests > 7000,
        },
        new_partners: {
          value: false,
          name: "количество партнеров увеличилось на 20%, значит появление новых партнеров = истина",
          rule: () => (this.partners[1] / this.partners[0]) * 100 >= 120,
        },
        low_demand: {
          value: false,
          name: "спрос на товар снизился на 40%, значит низкий спрос на товар = истина",
          rule: () => (this.demand_data[1] / this.demand_data[0]) * 100 <= 60,
        },
        weak_technique: {
          value: false,
          name: "отсутствует регламент по продажам ИЛИ нет регламента подтверждения встреч, значит слабые техники продаж = истина",
          rule: () => this.reg_appoint || this.reg_sell,
        },
        marketing_no_budget: {
          value: false,
          name: "затраты на маркетинг > бюджет на маркетинг, отсутствие бюджета на маркетинг = истина",
          rule: () => this.mark[0] > this.mark[1],
        },
      },
    };
  },
  methods: {
    calculate() {
      {
        this.result_strategy = "";
        this.find_strat = false;
        this.results = [];
        Object.values(this.conditions).forEach((condition) => {
          condition.value = false;
        });
        Object.values(this.conditions).forEach((condition) => {
          Object.values(this.strategies).forEach((strateg) => {
            if (strateg.rule() && !this.find_strat) {
              this.result_strategy = strateg.name2;
              this.results.push(strateg);
              this.find_strat = true;
            }
          });

          if (condition.rule() && !this.find_strat) {
            condition.value = true;
            this.results.push(condition);
          }
        });
      }
    },
    checkKey(evt) {
      const key = evt.key;
      const keysAllowed = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];

      if (!keysAllowed.includes(key)) {
        evt.preventDefault();
      }
    },
  },
};
</script>

<template>
  <form style="width: 100%" @submit.prevent>
    <table>
      <tr>
        <th></th>
        <th>Текущий месяц</th>
        <th>Предыдущий месяц</th>
      </tr>
      <tr>
        <td>Кол-во конкурентов</td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="leave_data[1]"
          />
        </td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="leave_data[0]"
          />
        </td>
      </tr>
      <tr>
        <td>Количество клиентов</td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="number_clients"
          />
        </td>
      </tr>
      <tr>
        <td>Количество заказов</td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="demand_data[1]"
          />
        </td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="demand_data[0]"
          />
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
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="brand_requests"
          />
        </td>
      </tr>
      <tr>
        <td>Количество партнеров</td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="partners[1]"
          />
        </td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="partners[0]"
          />
        </td>
      </tr>
      <tr>
        <td>Посещаемость сайта</td>
        <td>
          <input
            type="text"
            @keypress="checkKey($event)"
            v-model="site_traffic"
          />
        </td>
      </tr>
      <tr>
        <td>Затраты на маркетинг</td>
        <td>
          <input type="text" @keypress="checkKey($event)" v-model="mark[0]" />
        </td>
      </tr>
      <tr>
        <td>Бюджет на маркетинг</td>
        <td>
          <input type="text" @keypress="checkKey($event)" v-model="mark[1]" />
        </td>
      </tr>
    </table>
    <div class="second">
      <div>
        <input type="checkbox" v-model="advertising" id="c1" />
        <label for="c1"
          >Конкурент обширно рекламировал товар по низким ценам</label
        >
      </div>
      <div>
        <input type="checkbox" v-model="development" id="c2" />
        <label for="c2">В отрасли развиваются информационные технологии</label>
      </div>
      <div>
        <input type="checkbox" v-model="low_temp" id="c3" />
        <label for="c3">низкий темп роста экономики</label>
      </div>
      <div>
        <input type="checkbox" v-model="reg_sell" id="c4" />
        <label for="c4">отсутствует регламент продаж</label>
      </div>
      <div>
        <input type="checkbox" v-model="reg_appoint" id="c5" />
        <label for="c5">отсутствует регламент встреч</label>
      </div>
      <div>
        <input type="checkbox" v-model="postavschic_problems" id="c6" />
        <label for="c6">проблемы с поиском новых поставщиков</label>
      </div>
    </div>
    <button style="border: 1px solid; margin: 14px" @click="calculate">
      Узнать стратегию
    </button>
  </form>
  <div>
    <div style="border-top: 1px solid; padding: 5px" v-for="result in results">
      {{ result.name }}
    </div>
    <b style="font-size: 1.5rem">{{ result_strategy }}</b>
  </div>
</template>

<style>
form {
  display: grid;
  gap: 9px 20px;
  width: 900px;
  justify-content: center;
  align-items: center;
  grid-template-columns: 2fr 1fr;
}

/* form > div > div {
  text-align: start;
  vertical-align: middle;
} */

.second > div {
  text-align: start;
}

input[type="number"] {
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
