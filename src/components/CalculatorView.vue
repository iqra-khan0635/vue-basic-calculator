<template>
  <div class="calculator">
    <div class="display-text">{{ expression || "" }}</div>
    <div class="display-result">{{ currentValue || "" }}</div>
    <div
      v-for="(button, key) in buttons"
      :key="key"
      class="btn"
      :class="button.class ? button.class : ''"
      @click="button.click"
    >
      {{ button.label }}
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      currentValue: "",
      previousValue: "",
      expression: "",
      lastValue: "",
      isActionEqual: false,
    };
  },
  methods: {
    clear() {
      this.currentValue = "";
      this.previousValue = "";
      this.expression = "";
      this.lastValue = "";
      this.isActionEqual = false;
    },
    cancel() {
      if (this.currentValue) {
        this.currentValue = this.currentValue.slice(0, -1);
        this.expression = this.expression.slice(0, -1);
        this.isActionEqual = false;
      }
    },
    percentage() {
      this.currentValue = parseFloat(this.currentValue / 100).toString();
    },
    dot() {
      if (this.currentValue.indexOf(".") === -1) this.append(".");
    },
    append(value) {
      let newValue = value;

      if (this.isActionEqual) {
        if (!["+", "-", "*", "/", "="].includes(value)) {
          this.expression = "";
          this.currentValue = "";
        } else {
          this.expression = this.currentValue;
        }
        this.isActionEqual = false;
      }

      if (["+", "-", "*", "/", "="].includes(value)) {
        this.currentValue = "";
        let lastChar = this.expression.charAt(this.expression.length - 1);
        if (["+", "-", "*", "/", "="].includes(lastChar))
          this.expression = this.expression.slice(0, -1);
      } else this.currentValue = `${this.currentValue}${value}`;

      this.previousValue = value;
      this.expression = `${this.expression}${newValue}`;
    },
    equal() {
      if (!this.expression || this.isActionEqual) return false;
      if (
        !["+", "-", "*", "/"].some((operator) =>
          this.expression.includes(operator)
        )
      )
        return false;
      let newExpression = this.expression;

      let lastChar = this.expression.charAt(this.expression.length - 1);
      if (["+", "-", "*", "/", "="].includes(lastChar)) {
        if (lastChar == "=") {
          newExpression = this.expression.slice(0, -1);
        } else {
          newExpression = `${this.expression}0`;
          this.expression = newExpression;
        }
      }
      this.append("=");
      this.isActionEqual = true;

      let ans = eval(newExpression);
      if (ans == "Infinity") {
        ans = "";
        this.expression = "";
      }
      this.currentValue = ans.toString();
    },
  },
  computed: {
    buttons() {
      return [
        { label: "C", click: this.clear },
        { label: "CE", click: this.cancel },
        { label: "%", click: this.percentage },
        {
          label: "/",
          click: this.append.bind(this, "/"),
          class: "operator",
        },
        { label: "7", click: this.append.bind(this, "7") },
        { label: "8", click: this.append.bind(this, "8") },
        { label: "9", click: this.append.bind(this, "9") },
        {
          label: "*",
          click: this.append.bind(this, "*"),
          class: "operator",
        },
        { label: "4", click: this.append.bind(this, "4") },
        { label: "5", click: this.append.bind(this, "5") },
        { label: "6", click: this.append.bind(this, "6") },
        {
          label: "-",
          click: this.append.bind(this, "-"),
          class: "operator",
        },
        { label: "1", click: this.append.bind(this, "1") },
        { label: "2", click: this.append.bind(this, "2") },
        { label: "3", click: this.append.bind(this, "3") },
        {
          label: "+",
          click: this.append.bind(this, "+"),
          class: "operator",
        },
        { label: "0", click: this.append.bind(this, "0"), class: "zero" },
        { label: ".", click: this.dot },
        { label: "=", click: this.equal, class: "operator" },
      ];
    },
  },
};
</script>
<style scoped>
.calculator {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, auto);
  margin: 0 auto;
  font-size: 20px;
  width: 400px;
}
.display-text {
  grid-column: 1/5;
  background-color: #ccc;
  color: #000;
  text-align: right;
  font-size: 20px;
  padding: 8px 5px 2px 0px;
}
.display-result {
  grid-column: 1/5;
  background-color: #000;
  color: #fff;
  text-align: right;
  padding: 8px 5px 2px 0px;
}
.btn {
  background-color: #f8f8f8;
  border: 1px solid #ccc;
  padding: 2px;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}
.operator {
  background-color: #ffa500;
  color: #fff;
}
.zero {
  grid-column: 1/3;
}
</style>
