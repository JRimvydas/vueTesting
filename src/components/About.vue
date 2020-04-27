<template>
  <div class="about">
    <div class="overlay">
      <img src="@/assets/logo.png" alt="training" />
      <h1 class="title">Registruokis treniruotei internetu !</h1>
      <p>
        Karantino laikotarpiu išbandyk individualias treniruotes internetu,
        ofise ar Europos sporto klube. Patogiai ir efektyviai!/p>
      </p>

      <p id="company">
          MB Inovatyvaus sporto agentūra <br/>
          Vytauto Žalakevičiaus g. 7-10, Vilnius <br />
          Tel.: +370 613 34443 <br />
          El. paštas: info@fitty.lt <br />
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: "About",
};
</script>

<style scoped>
img{
    height: 80px;
}
.title{
    color: white;
    font-weight: normal;
    margin-top: 1em;
}
.title:after{
    display: block;
    width: 40px;
    content: "";
    border-top: 3px solid white;
    margin-top: 1em;
}
p{
    color: white;
    font-size: 1.4em;
}
p#company{
display: none;
}
.about{
    background: url("../assets/logotipas.jpg");
    background-size: cover;
    text-align: center;
}
.overlay{
    min-height: 40vh;
    padding: 1em;

}
@media screen and (min-width: 768px){
    .overlay{
        min-height: 100vh;
        position: relative;
        padding: 3em;
    }
    .about{
        text-align: left;
    }
    p#company{
        position: absolute;
        bottom: 2em;
        right: 2em;
        display: block;
        font-size: 0.9em;
        font-weight: lighter;
    }
}
</style>
