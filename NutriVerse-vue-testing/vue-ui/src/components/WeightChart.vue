<template>
  <div>
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script>
import { Line } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, LineElement, PointElement, LinearScale, CategoryScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, LineElement, PointElement, LinearScale, CategoryScale)

export default {
  name: 'WeightChart',
  props: {
    data: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      chart: null // Proprietà per mantenere l'istanza del grafico
    };
  },
  watch: {
    data: {
      immediate: true,
      handler(newData) {
        this.renderChart(newData)
      }
    }
  },
  methods: {
    renderChart(data) {
      if (this.chart) {
        this.chart.destroy(); // Distruggi il grafico esistente
      }

      if (this.$refs.canvas) {
        const ctx = this.$refs.canvas.getContext('2d')
        this.chart = new ChartJS(ctx, {
          type: 'line',
          data: {
            labels: data.map(entry => entry.date),
            datasets: [
              {
                label: 'Weight',
                backgroundColor: '#4d8330',
                data: data.map(entry => entry.value)
              }
            ]
          },
          options: {
            plugins: {
              title: {
                display: true,
                text: 'Weight Chart',
                font: {
                  size: 26, // Imposta la dimensione del carattere desiderata
                  weight: 'bold' // Opzionale: imposta il peso del carattere
                }
              },
              tooltip: {
                titleFont: {
                  size: 20 // Dimensione del carattere per il titolo del tooltip
                },
                bodyFont: {
                  size: 14 // Dimensione del carattere per il corpo del tooltip
                }
              }
            },
            responsive: true,
            maintainAspectRatio: true,
            scales: {
              y: {
                ticks: {
                  font: {
                    size: 14 // Dimensione del carattere per le etichette sull'asse Y
                  }
                }
              },
              x: {
                ticks: {
                  font: {
                    size: 14 // Dimensione del carattere per le etichette sull'asse X
                  }
                }
              }
            }
          }
        });

      }
    }
  },
  mounted() {
    this.renderChart(this.data)
  },
}
</script>

<style scoped>
/* Aggiungi eventuali stili qui */
</style>




