<script>
import { Line } from 'vue-chartjs';

export default {
    extends: Line,
    props: {
        amountOfPoints: Number,
        deltaX: Number,
        sourceYArray: Array,
        resultYArray: Array
    },
    mounted() {
        const newLabels = [];
        
        let currentX = 0;
        newLabels.push(currentX.toString());
        for(let i = 1; i < this.amountOfPoints; i++) {
            currentX += this.deltaX;
            newLabels.push(currentX.toString());
        }
        this.renderChart({
        labels: newLabels,
        datasets: [
            {
                label: 'Source Y',
                data: this.sourceYArray
            },
            {
                label: 'ResultY',
                data: this.resultYArray
            }
        ]
        }, {responsive: false, maintainAspectRatio: true});
    }
}
</script>