<!--Midterm Exam Options API to Composition API-->
<!--Student Name: Huzaifa Shoeb, ID 1925670-->

<template>
    <div>
      <form @submit.prevent="fetchRemoteData">
        <div class="form-group">
          <h2>Weather Data</h2>
          <p>Please fill in the form and use the submit button to load data.</p>
          <label for="">Location</label>
          <br />
          <input class="form-control rounded" v-model="formInput.location" type="text" required />
        </div>
  
        <div class="form-group">
          <label for="">Start Date</label>
          <br />
          <input class="form-control rounded" type="date" v-model="formInput.startDt" required />
        </div>
  
        <div class="form-group">
          <label for="">End Date</label>
          <br />
          <input class="form-control rounded" type="date" v-model="formInput.endDt" required />
        </div>
        <br />
        <div>
          <button class="btn btn-danger" type="submit">Submit</button>
        </div>
      </form>
      <br /><br />
  
      <div v-if="dataLoaded">
        <Bar :data="chartData" :options="chartOptions" />
      </div>
  
      <div v-if="dataLoaded">
        <hr />
        <h5>Weather Data in a Table</h5>
        <table class="table">
          <thead>
            <tr>
              <th v-for="adate in keys" :key="adate">{{ adate }}</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td v-for="atemp in values" :key="atemp">{{ atemp }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </template>
  
  <style>
  table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
  }
  </style>
  

  <!--Using the ref functions for composition API-->
  <script>
  import { ref } from 'vue';
  import axios from 'axios';
  
  import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    BarElement,
    CategoryScale,
    LinearScale
  } from 'chart.js';
  import { Bar } from 'vue-chartjs';
  
  ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend);
  
  export default {
    components: {
      Bar,
    },
    setup() {
      // Reactive form input data
      const formInput = ref({
        startDt: '',
        endDt: '',
        location: '',
      });
  
      // Chart options
      const chartOptions = {
        responsive: true,
        maintainAspectRatio: true,
        scales: {
          x: {
            title: {
              display: true,
              text: 'Time',
            },
          },
          y: {
            title: {
              display: true,
              text: 'Temperature (F)',
            },
          },
        },
      };
  
      // Reactive chart data
      const chartData = ref({
        labels: [],
        datasets: [
          {
            label: 'Average Temperature',
            backgroundColor: '#f87979',
            data: [],
          },
        ],
      });
  
      const dataLoaded = ref(false);
      const keys = ref([]);
      const values = ref([]);
  
      // Function to fetch remote data which will be used with setup() for composition API
      const fetchRemoteData = async () => {
        const options = {
          method: 'GET',
          url: 'https://weatherapi-com.p.rapidapi.com/history.json',
          params: {
            q: formInput.value.location,  //Instead of using "This" from Options API, we use formInput.value
            dt: formInput.value.startDt,
            end_dt: formInput.value.endDt,
            lang: 'en',
          },
          headers: {
            'X-RapidAPI-Key': '6c6f790650msh5ce4da2fced77a7p1b1776jsn247d9f531b74',
            'X-RapidAPI-Host': 'weatherapi-com.p.rapidapi.com',
          },
        };
  
        try {
          const resp = await axios.request(options);
  
          const wData = resp.data.forecast.forecastday;
          keys.value = wData.map((item) => item.date);
          values.value = wData.map((item) => item.day.avgtemp_f);
  
          chartData.value = {
            labels: keys.value,
            datasets: [
              {
                label: 'Average Temperature',
                backgroundColor: '#f87979',
                data: values.value,
              },
            ],
          };
  
          dataLoaded.value = true;
        } catch (error) {
          console.error('Error fetching weather data:', error);
        }
      };
  

      //Using the setup() functionality of composition API which return the values of variables used in the template
      return {
        formInput,
        chartOptions,
        chartData,
        dataLoaded,
        keys,
        values,
        fetchRemoteData,
      };
    },
  };
  </script>
  
