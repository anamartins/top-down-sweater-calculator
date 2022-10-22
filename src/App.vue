<template>
  <div class="wrapper">
    <header>
        <h1>Top Down Sweater Calculator</h1>
    </header>
  
    <section class="sample">
      <h2>Sample</h2>
      <div class="form" id="form-sample">
        <NumberInput label="Rows:" unit=" " v-model:value="sample.rows" id="rows" />
        <NumberInput label="Stitches:" unit=" " v-model:value="sample.stitches" id="stitches" />
      </div>
    </section>

    <section class="measures">
      <h2>Measures</h2>
      <div class="form" id="form-measures">
        <NumberInput label="Neckline:" v-model:value="measures.neckline"/>
        <NumberInput label="Armhole:" v-model:value="measures.armhole"/>
        <NumberInput label="Shoulder to shoulder:" v-model:value="measures.carrure"/>
        <NumberInput label="Chest:" v-model:value="measures.chest"/>
        <NumberInput label="Biceps:" v-model:value="measures.biceps"/>
        <NumberInput label="Wrist:" v-model:value="measures.wrist"/>
        <NumberInput label="Arm:" v-model:value="measures.arm"/>
      </div>
    </section>
    
    <section class="results" v-if="shouldShowResults">
      <h2>Results</h2>
        <p>Cast on <span class="number-result">{{results.neckline}}</span> stitches and make a circle. Make your collar using stitch and knit as many rows
        as you want. Your yoke will have <span class="number-result">{{results.armholeRows}}</span> rows. It means you need to make
        {{results.increaseCycles}} increase cycles (1 increasing row and {{details.perRow - 1}} regular row). In your increasing rows, you have
        to knit one extra stitch every <span class="number-result">{{results.yokeIncrease.every2}}</span> stitches. You'll end with
        <span class="number-result">{{results.armholeEnd}}</span> stitches.</p>
        <p>Now, the separation: the body will have <span class="number-result">{{results.body}}</span> stitches. So:</p>
          <ol>
            <li>pick up <span class="number-result">{{results.yokeSleeves}}</span> stitches in a pin for the sleeves;</li>
            <li>cast on more <span class="number-result">{{results.differenceChestCarrure}}</span> stitches on the needle for the underarm part of the body;</li>
            <li>knit <span class="number-result">{{Math.ceil(results.body / 2)}}</span> stitches for the front part;</li>
            <li>put <span class="number-result">{{results.yokeSleeves}}</span> stitches in a pin for the other sleeve;</li>
            <li>cast on more <span class="number-result">{{results.differenceChestCarrure}}</span> stitches on the needle for the underarm part of the body;</li>
            <li>knit <span class="number-result">{{Math.floor(results.body / 2)}}</span> stitches for the the back part. Knit the body until the end. Go to the sleeves.
                Pass to the needle the <span class="number-result">{{results.yokeSleeves}}</span> stitches from the pin. Don't forget to add the 
                <span class="number-result">{{results.differenceChestCarrure}}</span> stitches for the underarm part of the sleeve.</li>
            <li>Make your sleeves with {{results.arm}} rows.</li>
          </ol>
        <p>Happy knitting!</p>
    </section>
</div>
</template>

<script> 
  import NumberInput from './components/number-input.vue';
   export default {
    components: { NumberInput },
    data(){
      return{
        sample:{
          rows: 0, 
          stitches: 0
        },
        measures:{
          chest: 0,
          biceps: 0,
          armhole: 0,
          carrure: 0,
          wrist: 0,
          neckline: 60,
          arm: 0,
        }, 
        details: {
          isYoke: true,
          isPuff: true,
          perRow: 2,
        }
      }
    },
    watch:{
      sample:{
        handler(newValue, oldValue){
          localStorage.setItem('sample',JSON.stringify(newValue));
        },
        deep: true
      },
      measures:{
        handler(newValue, oldValue){
          localStorage.setItem('measures',JSON.stringify(newValue));
        },
        deep: true
      },
    },
    mounted(){
      if (localStorage.getItem("sample")){
        let sample = JSON.parse(localStorage.getItem("sample"));
        this.sample = sample;
      }
      if (localStorage.getItem("measures")){
        let measures = JSON.parse(localStorage.getItem("measures"));
        this.measures = measures;
      }

    },
    computed:{
      results(){
        let neckline = (this.sample.stitches * this.measures.neckline) / 10;
        let armholeEndCm = 2 * this.measures.biceps + this.measures.chest;
        let armholeEndStitches = Math.round((this.sample.stitches * armholeEndCm) / 10)
        let armholeRows = Math.ceil(((this.measures.armhole + 3) * this.sample.rows) /10);

        if (armholeRows % 2 !== 0) {
          armholeRows++;
        }

        let increaseCycles = armholeRows / 2;

        let difference = Math.round(armholeEndStitches - neckline);

        let increasesPerRow = Math.round(difference/armholeRows);

        let differenceChestCarrureCm = this.measures.chest / 2 - this.measures.carrure;

        let differenceChestCarrureStitches = Math.round((differenceChestCarrureCm * this.sample.stitches) / 10);

        let yokeIncrease = {every2: neckline / (increasesPerRow * 2), every3: neckline / (increasesPerRow * 3), every4: neckline / (increasesPerRow * 4)};
        let yokeSleeves = Math.round((this.measures.biceps * this.sample.stitches) / 10);

        let body = armholeEndStitches - yokeSleeves - yokeSleeves;
        let raglan = Math.round(difference / 8);

        let arm = Math.round((this.sample.rows * this.measures.arm) / 10);

        let wrist = Math.round((this.measures.wrist * this.sample.stitches) /10);

        return {
          neckline, 
          armholeEnd: armholeEndStitches,
          increaseCycles,
          difference,
          increasesPerRow,
          differenceChestCarrure: differenceChestCarrureStitches,
          yokeIncrease,
          yokeSleeves,
          body,
          raglan,
          wrist,
          arm,
          armholeRows
        };
      },
      shouldShowResults(){
        let {
          chest,
          biceps,
          armhole,
          carrure,
          wrist,
          neckline,
          arm,
        } = this.measures;
        let {rows, stitches} = this.sample;
        return [rows, 
          stitches, 
          chest,
          biceps,
          armhole,
          carrure,
          wrist,
          neckline,
          arm,].every((currentValue) => parseInt(currentValue) > 0); 
      }
    }
   }
</script>

<style lang="scss" scoped>
  * {
    margin: 0;
    box-sizing: border-box;
    font-family: "Roboto", sans-serif;
    font-weight: 400;
  }

  .wrapper{
    display: flex;
    flex-flow:row wrap;
    align-items: flex-start;
    justify-content: flex-start;
  }

  header{
    position: relative ;
    width: 100%;
    margin: 0 0 10px 0;
  }

  h1{
    font-family: "Roboto Serif", serif;
    font-size: 4rem;
    margin:0;
    line-height: 90px;
  }
  
  section{
    position: relative;
  }
  .sample{
    width: 35%;
  }
  .measures{
    width: 65%;
  }
  .results{
    width: 90%;
    font-size: 1.2rem;
  }
  .number-result{
    font-weight: 700;
  }
  #rows,
  #stitches{
    min-width: 110px;
  }
  h2{
    font-family: "Roboto", sans-serif;
    font-weight: 500;
    font-size: 2rem;
    background-color: var(--fluorgreen);
    padding: 0px;
    margin: 15px 0px;
    width: 50%;
  }
  .form{
    position: relative;
    width:100%;
    position: relative;
    display: flex;
    flex-flow: row wrap;
    width: 100%;
  }  
  @media only screen and (max-width: 768px) {
    .sample, .measures, .results{
      width: 100%;
    } 
  }
</style>