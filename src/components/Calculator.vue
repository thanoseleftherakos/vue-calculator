<template>
    <div class="calculator">
        <div class="calculator__inner">
            <div class="calculator__screen">
                <div class="calculator__screen__history">{{ calculator.formula }}</div>
                <div class="calculator__screen__result" :style="{fontSize: display_scale + 'em'}">{{ calculator.display_value }}</div>
            </div>
            <div class="calculator__buttons">
                <div class="calculator__buttons__item" tabindex="1" @click="reset"><span>C</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_sign('±')"><span>±</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_math('%')"><span>%</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_math('√')"><span>√</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(7)"><span>7</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(8)"><span>8</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(9)"><span>9</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_operator('/')"><span>÷</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(4)"><span>4</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(5)"><span>5</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(6)"><span>6</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_operator('*')"><span>×</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(1)"><span>1</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(2)"><span>2</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(3)"><span>3</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_operator('-')"><span>−</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input(0)"><span>0</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_input('00')"><span>00</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_dot"><span>,</span></div>
                <div class="calculator__buttons__item" tabindex="1" @click="handle_operator('+')"><span>+</span></div>
            </div>
            <button class="calculator__equals" @click="handle_equal()"><span>=</span></button>
        </div>
    </div>
</template>

<script>
export default {
  name: "Calculator",
  data() {
    return {
      calculator: {
        current_operator: null,
        current_value: "",
        display_value: 0,
        finished: true,
        math: null,
        total_value: 0,
        formula: " "
      }
    };
  },
  mounted() {
    window.addEventListener("keypress", e => {
      let keyCode = e.keyCode || e.which;
      let num = parseInt(String.fromCharCode(keyCode), 10);
      if (keyCode == 13) {
        this.handle_equal();
      } //enter keyboard
      if (isNaN(num)) {
        let operator = String.fromCharCode(keyCode);
        const oparators = ["+", "-", "/", "%", "√", "%"];
        if (oparators.find(k => k == operator)) {
          this.handle_operator(operator);
        }
      } else {
        this.handle_input(num);
      }
    });
  },
  computed: {
    display_scale() {
      if (this.calculator.display_value.length >= 9) {
        return 4.2 - this.calculator.display_value.length / 9;
      } else {
        return 4.2;
      }
    }
  },
  methods: {
    reset() {
      this.calculator.current_operator = null;
      this.calculator.current_value = "";
      this.calculator.display_value = 0;
      this.calculator.formula = " ";
    },
    handle_operator(operator) {
      this.calculator.finished = false;
      if (!this.calculator.current_value && this.calculator.finished) return;

      if (this.calculator.current_operator) {
        this.calculate();
        this.calculator.current_operator = operator;
      } else {
        this.calculator.current_operator = operator;
        if (this.calculator.current_value) {
          this.calculator.total_value = this.calculator.current_value;
        }
      }
      this.calculator.formula = `${this.calculator.formula}${this.calculator.current_value} ${operator} `;
      this.calculator.current_value = "";
    },
    handle_sign() {
      if (this.calculator.current_value != "") {
        this.calculator.current_value = this.calculator.current_value * -1;
        this.calculator.display_value = this.calculator.current_value;
      } else if (this.calculator.total_value != "") {
        this.calculator.total_value = this.calculator.total_value * -1;
        this.calculator.display_value = this.calculator.total_value;
      }
    },
    handle_math(symbol) {
      switch (symbol) {
        case "√":
          this.calculator.total_value = this.calculator.total_value
            ? Math.sqrt(this.calculator.total_value).toFixed(3)
            : Math.sqrt(this.calculator.current_value).toFixed(3);
          break;
        case "%":
          this.calculator.total_value = this.calculator.total_value
            ? this.calculator.total_value / 100
            : this.calculator.current_value / 100;
          break;
      }
      this.calculator.formula = `${this.calculator.formula}${this.calculator.current_value} ${symbol} `;
      this.calculator.display_value = this.calculator.total_value;
      this.calculator.current_value = "";
    },
    handle_input(number) {
      if (this.calculator.finished) {
        this.calculator.formula = "";
      }
      this.calculator.current_value = `${this.calculator.current_value}${number}`;
      this.calculator.display_value = this.calculator.current_value;
    },
    handle_dot() {
      if (this.calculator.current_value.indexOf(".") === -1)
        this.handle_input(".");
    },
    handle_equal() {
      if (this.calculator.finished) return;
      this.calculate();
      this.calculator.formula = `${this.calculator.formula}${this.calculator.current_value}`;
      this.calculator.current_value = "";
      this.calculator.current_operator = null;
      this.calculator.finished = true;
    },
    calculate() {
      switch (this.calculator.current_operator) {
        case "+":
          this.calculator.math = (a, b) => a + b;
          break;
        case "-":
          this.calculator.math = (a, b) => a - b;
          break;
        case "*":
          this.calculator.math = (a, b) => a * b;
          break;
        case "/":
          this.calculator.math = (a, b) => a / b;
          break;
      }
      this.calculator.total_value = this.calculator.math(
        parseFloat(this.calculator.total_value),
        parseFloat(this.calculator.current_value)
      );
      this.calculator.display_value = this.calculator.total_value
        .toFixed(3)
        .replace(/[.,]000$/, "");
    }
  }
};
</script>

<style scoped lang="scss">
.calculator {
  position: relative;
  max-width: 400px;
  width: 100%;
  margin: 0 auto;
  @media only screen and (min-width: 577px) {
    padding: 40px;
    top: 50%;
    transform: translateY(-50%);
  }
  &__inner {
    background: #fff;
    box-shadow: 0px 10px 60px rgba(0, 0, 0, 0.2);
  }
  &__screen {
    position: relative;
    text-align: right;
    padding: 20px 20px 12px;
    min-height: 105px;
    display: flex;
    flex-wrap: wrap;
    &__history {
      color: #b3b3b3;
      min-height: 20px;
      flex: 0 1 100%;
    }
    &__result {
      font-size: 4.2em;
      font-weight: 300;
      padding-top: 5px;
      flex: 0 1 100%;
      align-self: flex-end;
    }
  }
  &__buttons {
    color: #fff;
    position: relative;
    background: rgb(43, 46, 58);
    background: linear-gradient(
      to bottom,
      rgba(43, 46, 58, 0.6),
      rgba(43, 46, 58, 0.85)
    );
    height: 400px;
    padding: 30px 0;
    font-size: 2em;
    @media only screen and (max-width: 576px) {
      padding: 10px 0;
      height: 350px;
    }
    @media only screen and (max-width: 320px) {
      padding: 10px 0;
      height: 300px;
      font-size: 1.7em;
    }
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    &:before,
    &:after {
      background: #55596c;
      background: linear-gradient(
        to bottom,
        rgba(43, 46, 58, 0.6),
        rgba(43, 46, 58, 0.85)
      );
      content: "";
      position: absolute;
      height: 100%;
      top: 0;
      width: 20px;
      @media only screen and (max-width: 576px) {
        display: none;
      }
    }
    &:before {
      left: -20px;
    }
    &:after {
      right: -20px;
    }
    &__item {
      box-shadow: 0px 1px 50px rgba(0, 0, 0, 0);
      transition: box-shadow 0.4s ease;
      cursor: pointer;
      outline: none;

      &:focus:not(:active) {
        animation: buttonPress 0.4s ease-out;
      }
      &:hover {
        transition: box-shadow 0.2s ease;
        box-shadow: 0px 1px 10px rgba(0, 0, 0, 0.3);
      }
      span {
        font-weight: 300;
        align-self: center;
      }
      display: flex;
      flex: 0 1 25%;
      justify-content: center;
    }
  }
  &__equals {
    position: relative;
    display: block;
    width: 100%;
    text-align: center;
    cursor: pointer;
    padding: 0px 0 7px;
    margin: 0;
    font-size: 3.2em;
    font-weight: 100;
    outline: none;
    border: none;
    color: #fff;
    box-shadow: 0px 1px 20px rgba(0, 0, 0, 0);
    transition: box-shadow 0.5s ease;
    background: linear-gradient(to left, #f2c94c, #f2994a);
    span {
      position: relative;
      z-index: 2;
    }
    &:before,
    &:after {
      position: absolute;
      content: "";
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;
      z-index: 1;
    }
    &:before {
      background: #f2994a;
      background: linear-gradient(to left, #f2c94c, #f2994a);
    }
    &:after {
      background: #f2c94c;
      opacity: 0;
      transition: opacity 0.5s ease;
    }
    &:hover {
      box-shadow: 0px 1px 20px rgba(0, 0, 0, 0.2);
      &:after {
        opacity: 1;
      }
    }
  }
}

@keyframes buttonPress {
  0% {
    background: rgba(242, 152, 74, 0);
  }
  50% {
    background: #f2994a;
  }
  100% {
    background: rgba(242, 152, 74, 0);
  }
}
</style>
