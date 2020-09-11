<template>
    <div>
        <div class="nav field has-addons has-addons-centered">
            <div class="control">
                <input class="input" type="number" v-model="duration" :disabled="!isStopped">
            </div>
            <div class="control">
                <button class="button" @click="handleStart()" v-if="isStopped"><font-awesome-icon icon="play" /></button>
                <button class="button is-danger" @click="handleReset()" v-if="!isStopped"><font-awesome-icon icon="step-backward" /></button>
            </div>
        </div>
        <div class="main has-background-dark">
            <div class="has-text-centered">
                <canvas id="clock-canvas" width="375" height="375">
                </canvas>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Watch, Prop, Vue } from "vue-property-decorator";
import Push from 'push.js'

@Component
export default class Timer extends Vue {
    @Prop() private msg!: string;

    private refreshIntervalId: any = {}
    private isStopped = true
    private element: any = {}
    private graphic: any = {}
    private radius: any = 0.0
    private startTime: any = new Date()
    private duration = 10  //minutes
    private restTime = 0 //seconds

    get hoge() {
        return Math.random()
    }

    @Watch('duration')
    onDurationChanged(value: number, oldValue: number) {
        this.restTime = value * 60
        this.renderClock()
    }

    @Watch('restTime')
    onRestTimeChanged(value: number, oldValue: number) {
        if (value < 0) {
            clearInterval(this.refreshIntervalId);
            this.isStopped = true
            this.restTime = this.duration * 60
            Push.create(`Finished. Duration: ${this.duration} mins.`)
            this.renderClock()
        }
    }
    
 // Lifecycle hook
    mounted () {
        this.element = document.getElementById("clock-canvas");
        this.graphic = this.element.getContext("2d");
        this.radius = this.element.height / 2;
        this.graphic.translate(this.radius, this.radius);
        this.restTime = this.duration * 60
        this.renderClock()
    }
    StartTimer(): void {
        this.isStopped = false 
        this.renderClock()
        this.restTime -= 1
    }
    renderClock(): void {
        // face
        this.graphic.beginPath();
        this.graphic.arc(0, 0, this.radius, 0, 2 * Math.PI);
        this.graphic.fillStyle = 'white';
        this.graphic.fill();

        this.graphic.stroke();
        this.graphic.beginPath();
        this.graphic.arc(0, 0, this.radius * 0, 0, 2 * Math.PI);
        this.graphic.fillStyle = '#363636';
        this.graphic.fill();

        // numbers
        let angle: any;
        let number: any;
        this.graphic.font = this.radius * 0.10 + "px arial";
        this.graphic.textBaseline = "middle";
        this.graphic.textAlign = "center";
        for (number = 1; number < 13; number++) {
            angle = number * Math.PI / 6;
            this.graphic.rotate(angle);
            this.graphic.translate(0, - this.radius * 0.85);
            this.graphic.rotate(-angle);
            const faceNumber = 60 - number * 5
            this.graphic.fillText(faceNumber.toString(), 0, 0);
            this.graphic.rotate(angle);
            this.graphic.translate(0, this.radius * 0.85);
            this.graphic.rotate(-angle);
        }

        // hand
        const count = (45-this.restTime/60) * Math.PI / 30;

        this.graphic.beginPath();
        this.graphic.moveTo(0, 0);
        this.graphic.arc(0, 0, this.radius*0.7, count, 1.5*Math.PI);
        this.graphic.fillStyle = '#F34667';
        this.graphic.fill();

    }

    handleStart(): void {
        this.restTime = this.duration * 60
        this.refreshIntervalId = setInterval(this.StartTimer, 1000);
        setTimeout(this.StartTimer, 0)
    }

    handleReset(): void {
        clearInterval(this.refreshIntervalId);
        this.restTime = this.duration * 60
        this.renderClock()
        this.isStopped = true
    }
}



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .nav {
        padding-top: 10px;
    }
    .main {
        padding-top: 30px;
        height: 100vh;
    }
    #clock-canvas {
        padding-top: 10vh;
        background-color: #363636;
    }
</style>
