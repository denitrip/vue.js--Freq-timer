<template>
  <div>
    <div class="row">
      <div class="col-md-1 offset-4">
        <button type="button" class="btn btn-success" v-if="showControls && showStart" @click="start">Start</button>
      </div>
      <div class="col-md-1">
        <button type="button" class="btn btn-success" v-if="showControls" @click="pause">Halt</button>
      </div>
      <div class="col-md-1">
        <button type="button" class="btn btn-success" v-if="showControls" @click="resume">Resume</button>
      </div>
      <div class="col-md-1">
        <button type="button" class="btn btn-danger" v-if="showControls" @click="quit">Quit</button>
      </div>
    </div>
    <div class="row margin-top-5" v-if="showInfoLabel">
      <div class="col-md-2 offset-2">
        <input  v-show="!isFrequencySet" v-model="frequency" type="number" class="form-control" placeholder="enter freq"/>
      </div>
      <div class="col-md-3">
        <input v-model="userData" type="number" class="form-control" placeholder=""/>
      </div>
      <div class="col-md-1">
        <button type="button" class="btn" @click="submitNumber">Submit</button>
      </div>
    </div>
    <div class="row tasks" v-if="showInfoLabel">
      <div class="col-md-3 offset-4 margin-top-5">
        Please enter the number and press submit
      </div>
    </div>
    <div class="row margin-top-5" v-if="!showInfoLabel && showControls">
      <div class="col-md-3 offset-4">
        <input v-model="showTime" :disabled="true" class="form-control" placeholder=""/>
      </div>
    </div>
    <div class="row margin-top-5" v-if="!showControls">
      <div class="col-md-3 offset-4 margin-top-5">
        You have entered: {{showTime}}
      </div>
    </div>
    <div class="row margin-top-5" v-if="!showControls">
      <div class="col-md-3 offset-4 margin-top-5">
        Thanks for playing, press F5 to start again
      </div>
    </div>
  </div>
</template>

<script>

  import Vue from 'vue';
  import {
    handlerResponse,
    setNotifyData
  } from '../../helpers/commonHelpers.js';

  export default {
    data() {
      return {
        numbersList: {},
        userData: '',
        sortedData: [],
        status: false,
        showInfoLabel: true,
        isFrequencySet: false,
        showControls: true,
        isPaused: false,
        showStart: true,
        showTime: '',
        frequency: ''
      }
    },
    methods: {
      start() {
        if (!this.isFrequencySet) {
          Vue.notify(setNotifyData('Error', 'Set frequency', 'Error'))
        }
        else {
          this.showInfoLabel = false;
          this.showStart = false;
          this.prepareNumbers();
          this.launchTimer();
        }
      },
      submitNumber() {
        if (this.frequency) {
          this.isFrequencySet = true;
        }
        if (this.userData) {
          if (this.userData < 43466557686937456435688527675040625802564660517371780402481729089536555417949051890403879840079255169295922593080322634775209689623239873322471161642996440906533187938298969649928516003704476137795166849228875) {
             if (this.FibonacciChecker(this.userData)) {
               Vue.notify(setNotifyData('Error', 'FIB', 'Error'))
             }
             else {
               this.addNumber(this.userData);
             }
          }
          else this.addNumber(this.userData);
        }
        else {
          Vue.notify(setNotifyData('Error', 'Please enter the number', 'Error'))
        }
      },
      addNumber(num) {
        this.userData = '';
        Vue.notify(setNotifyData('Success', 'Number has been added', 'Success'))
        if (this.numbersList[num]) {
          this.numbersList[num]++;
        }
        else this.numbersList[num] = 1
      },
      prepareNumbers() {
        let sortedNumbers = [];
        for (let number in this.numbersList) {
          sortedNumbers.push([number, this.numbersList[number]]);
        }
        this.sortedData = sortedNumbers.sort((a, b) => b[1] - a[1]).map(num => num.join(':'));
      },
      launchTimer(){
        this.showTime = this.sortedData.shift();
        let timer = setInterval(
          () => {
            if (this.sortedData.length === 0) {
              this.showTime = '';
              clearInterval(timer);
            }
            else if (!this.isPaused){
              this.showTime = this.sortedData.shift();
            }
          }, (this.frequency * 1000))
      },
      pause() {
        this.isPaused = true;
      },
      resume() {
        this.isPaused = false;
      },
      quit() {
        this.pause();
        this.showInfoLabel = false;
        this.showControls = false;
        this.prepareNumbers();
        if (this.sortedData && this.sortedData.length) {
          this.showTime = this.sortedData.join(', ');
        }
      },
      FibonacciChecker(val) {
        let prev = 0;
        let curr = 1;
        while (prev <= val) {
          if (prev == val) {
            return true
          }
          curr = prev + curr;
          prev = curr - prev;
        }
        return false;
      }
    }
  }
</script>

<style scoped>

</style>
