<template>
  <div id="app">
    <div class="d-flex">
      <PlotChart
        :deltaX="deltaX"
        :amountOfPoints="amountOfPoints"
        :sourceYArray="sourceYArray"
        :resultYArray="resultYArray"
      />

      <h4 class="mx-auto my-auto">
        Mean Approximation Error: {{ approximationError }}
      </h4>
    </div>

      <b-table stripped hover :items="items"></b-table>
  </div>
</template>

<script>

import PlotChart from './components/PlotChart';

export default {
  name: 'app',
  components: {
    PlotChart
  },
  data() {
    return {
      deltaX: null,
      amountOfPoints: null,
      sourceYArray: [],
      resultYArray: [],

      a: null,
      b: null,

      yErrors: [],
      approximationError: null,

      items: null
    }
  },
  mounted() {
    this.calculateSmallestSquares();
  },
  methods: {
    calculateSmallestSquares() {
      const leftBorder = 0;
      const rightBorder = 1;

      const amountOfPoints = 15;
      const deltaX = (rightBorder - leftBorder) / (amountOfPoints - 1);

      const xArray = new Array(amountOfPoints);
      const yArray = new Array(amountOfPoints);
      const resultYArray = new Array(amountOfPoints);
      const deltaArray = new Array(amountOfPoints);

      let squareXSum = 0;
      let xSum = 0;
      let ySum = 0;
      let yxSum = 0;
      
      let currentX = leftBorder;
      xArray[0] = currentX;
      yArray[0] = this.calculateSourceFunction(currentX);
      squareXSum += Math.pow(xArray[0], 2);
      ySum += yArray[0];
      xSum += xArray[0];
      yxSum += xArray[0] * yArray[0];
      console.log('XYSUM:-----' );
      console.log(yxSum);

      for(let i = 1; i < amountOfPoints; i++) {
        currentX += deltaX;
        xArray[i] = currentX;
        yArray[i] = this.calculateSourceFunction(currentX);

        squareXSum += Math.pow(xArray[i], 2);

        xSum += xArray[i];
        ySum += yArray[i];
        yxSum += yArray[i] * xArray[i];
        console.log(yxSum);
      }

      console.log('XYSUM--------');
      console.log(xArray);
      console.log(yArray);

      console.log(squareXSum);
      console.log(xSum);
      console.log(ySum);
      console.log(yxSum);

      this.a = (yxSum - (xSum / amountOfPoints) * ySum) / 
        (squareXSum - (xSum * xSum / amountOfPoints));
      this.b = (ySum - (yxSum * xSum/squareXSum)) / (amountOfPoints - (xSum*xSum/squareXSum));

      this.sourceYArray = yArray;
      this.resultYArray = [];
      for(let i = 0; i < amountOfPoints; i++) {
        this.resultYArray.push(this.calculateResultFunction(xArray[i]));
      }
      console.log(this.resultYArray);
      console.log(this.sourceYArray);

      this.amountOfPoints = amountOfPoints;
      this.deltaX = deltaX;

      this.yErrors = [];
      let errorSum = 0;
      for(let i = 0; i < amountOfPoints; i++) {
        this.yErrors.push(Math.pow((this.sourceYArray[i] - this.resultYArray[i]),2));
        errorSum += this.yErrors[i];
      }

      this.approximationError = errorSum/amountOfPoints;

      this.items = [];
      for(let i = 0; i < amountOfPoints; i++) {
        this.items.push({
          sourceYi: yArray[i],
          resultYi: this.resultYArray[i],
          deltaY: this.resultYArray[i] - yArray[i],
          squareError: Math.pow((this.resultYArray[i] - yArray[i]), 2)
        })
      }
    },

    calculateSourceFunction(x) {
      return Math.pow(Math.cos(3*x), 2) + Math.pow((1 - Math.pow(x, 4)), 0.5);
    },

    calculateResultFunction(x) {
      return this.a * x + this.b; 
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
