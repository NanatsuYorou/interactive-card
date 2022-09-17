<template>
  <div id="app">
    <main class="wrapper">
    <section class="card__container">
      <div class="card-back">
        <p class="card__text small" v-if="this.cvc">{{this.cvc}}</p>
        <p class="card__text small" v-else>000</p>
      </div>
      <div class="card-front">
        <div class="card__info">
          <img src="./images/card-logo.svg" alt="">
          <p class="card__text" v-if="this.cardnumber">{{this.cardnumber}}</p>
          <p class="card__text" v-else>0000 0000 0000 0000</p>
          <div class="card__footer">
            <p class="card__text small" v-if="this.cardholder">{{this.cardholder}}</p>
            <p class="card__text small" v-else>jane appleseed</p>
            <p class="card__text small" v-if="this.year && this.month">{{this.month}}/{{this.year}}</p>
            <p class="card__text small" v-else>00/00</p>
          </div>
        </div>
      </div>
    </section>
    <section class="form"
    v-if="!submited"
    >
      <form action="">
        <div class="form__input">
          <label for="">Cardholder Name</label>
          <input class="input" type="text" name="cardholder" placeholder="e.g. Jane Appleseed" v-model="cardholder" @input="hideNotification($event.target)" @change="validation($event.target)">
          <span class="notification cardholder visually-hidden"></span>
        </div>
        <div class="form__input">
          <label for="">Card Number</label>
          <input class="input" type="text" name="cardnumber" placeholder="e.g. 1234 5678 9123 0000" v-model="cardnumber" @input="hideNotification($event.target)" @change="validation($event.target)">
          <span class="notification cardnumber visually-hidden"></span>
        </div>
        <div class="form__footer">
          <div class="form__input">
            <label for="">Exp. Date</label>
            <input class="input" type="text" name="month" placeholder="MM" v-model="month" @input="hideNotification($event.target)" @change="validation($event.target)">
            <span class="notification month visually-hidden"></span>
          </div>
          <div class="form__input">
            <label for="">(MM/YY)</label>
            <input class="input" type="text" name="year" placeholder="YY" v-model="year" @input="hideNotification($event.target)" @change="validation($event.target)">
            <span class="notification year visually-hidden"></span>
          </div>
          <div class="form__input">
            <label for="">CVC</label>
            <input class="input" type="text" name="cvc" placeholder="e.g. 123" v-model="cvc" @input="hideNotification($event.target)" @change="validation($event.target)">
            <span class="notification cvc visually-hidden"></span>
          </div>
        </div>
        <div class="form__input">
          <input class="button submit" type="submit" name="enter" value="Confirm" @click.prevent="submit()">
          <span class="notification enter visually-hidden"></span>
        </div>
      </form>
    </section>

    <section class="complete"
    v-else
    >
      <img src="@/images/icon-complete.svg" alt="">
      <h3 class="heading large">Thank you!</h3>
      <p class="big gray">We've added your card details</p>
      <a href="https://clck.ru/3vyXS" target="_blank"><button class="button submit">Continue</button></a>
    </section>
    <span class="notification"></span>
  </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      cardholder: '',
      cardnumber: '',
      month: '',
      year: '',
      cvc: '',
      submited: false,
    }
  },
  methods: {
    submit(){
      let errors = false
      let input_array = document.querySelectorAll('input')
      for(let i = 0; i < input_array.length; i++){
        if(!this.validation(input_array[i])){
          errors = true
        }
      }

      if(!errors){
        this.submited = true
      }    
    },
    getCoords(elem) {
      let box = elem.getBoundingClientRect();

      return {
        top: box.top + window.pageYOffset,
        right: box.right + window.pageXOffset,
        bottom: box.bottom + window.pageYOffset,
        left: box.left + window.pageXOffset
      };
    },
    showNotification(input, coords, text){
      let notification = document.querySelector('.'+input.name)
      notification.textContent = text
      notification.classList.remove('visually-hidden')
    },
    hideNotification(input){
      let notification = document.querySelector('.'+input.name)
      notification.classList.add('visually-hidden')
    },
    validation(input){
      let notificationShown = false
      let notificationText
      switch(input.name){
        case 'cardholder': {
          let nameRegex = /^((?=[a-z \-']).)+$/i;
          if(!this.cardholder.match(nameRegex)){
            notificationText = "Only characters A-Z, a-z and '-' are  acceptable."
            notificationShown = true
            this.cardholder = ''
          } else if(this.cardholder.trim().length == 0){
            notificationText = "Can't be blank."
            notificationShown = true
          }
        } break;
        case 'cardnumber': {
          let array = this.cardnumber.split(' ')
          let formatErr = false
          let numbersErr = false
          if (this.cardnumber !== '')
            {
              if (array.length !== 4){
              formatErr = true
            } 
              for(let i=0; i < array.length; i++){
                if(array[i].length !== 4)
                  formatErr = true
                for(let j = 0; j < array[i].length; j++){
                  if(Number.isNaN(parseInt(array[i][j]))){
                    numbersErr = true
                  }
                }
              }
            } else{
              notificationText = "Can't be blank. "
              notificationShown = true
            }
          if (formatErr){
            notificationText = "Wrong format. "
            notificationShown = true
          }
          if (numbersErr){
            notificationText += "Numbers only."
            notificationShown = true
          }
        } break;
        case 'month': {
          let month = this.month

          if(month.length == 0){
            notificationText = "Can't be blank. "
            notificationShown = true
          } else
          for (let i = 0; i < month.length; i++) {
            if(Number.isNaN(parseInt(month[i]))){
              notificationText = "Wrong type/value"
              notificationShown = true
            }
            if(parseInt(month) <= 0 || parseInt(month) > 12){
              notificationText = "Wrong value"
              notificationShown = true
            }
            if (month.length == 1)
            {
              this.month = '0' + month
            }
          }
        } break;
        case 'year': {
          let year = this.year
          if(year.length == 0){
              notificationText = "Can't be blank. "
              notificationShown = true
          } else {
            for (let i = 0; i < year.length; i++) {
              if(Number.isNaN(parseInt(year[i]))){
                notificationText = "Wrong type/value"
                notificationShown = true
              }
              if(parseInt(year) <= 0 || parseInt(year) > 99){
                notificationText = "Wrong type/value"
                notificationShown = true
              }
            }
          }
        } break;
        case 'cvc': {
          let cvc = this.cvc
          if(cvc.length == 3){
            for (let i = 0; i < cvc.length; i++) {
              if(Number.isNaN(parseInt(cvc[i]))){
                notificationText = "Wrong value"
                notificationShown = true
              }
            }
          } else if(cvc.length == 0){
            notificationText = "Can't be blank"
            notificationShown = true
          } else {
            notificationText = "Wrong value"
            notificationShown = true
          }
          
        } break;
        // default: {} break;
      }
      if(notificationShown){
          let coords = this.getCoords(input);
          this.showNotification(input, coords, notificationText)
          return false
        }
        else{
          this.hideNotification(input)
          return true
        }
    }
  },
}
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

html, body{
    width: 100%;
    height: 100%;
    margin: 0;
}

.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  margin: -1px;
  border: 0;
  padding: 0;
  white-space: nowrap;
  clip-path: inset(100%);
  clip: rect(0 0 0 0);
  overflow: hidden;
}

.wrapper{
    font-family: sans-serif;
    font-size: 12px;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
}

.card{
    &__text{
        margin: 0;
        font-size: 16px;
    }
    &__container{
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;
        background-image: url('./images/bg-main-mobile.png');
        background-size: 100% 100%;
        background-repeat: no-repeat;
        width: 100%;
        height: 240px;
        padding-top: 10%;
        height: calc((100vw - 20px) * 0.5625);
        color: #fff;
        text-transform: uppercase;
    }
    &__footer{
        display: flex;
        justify-content: space-between;
    }
    &-front{
        position: absolute;
        right: calc(50% - 10vw - 50px);
        top: calc(((100vw - 20px) * 0.5625)*0.5 + 10px);
        background-image: url('./images/bg-card-front.png');
        background-size: 100% 100%;
        background-repeat: no-repeat;
        width: 70%;
        height: 70%;
        max-width: 290px;
        max-height: 160px;
        img{
            width: 40px;
            height: auto;
        }
    }
    &__info{
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        width: 90%;
        height: 60%;
        margin: 0 auto;
        margin-top: 5%;
    }
    &-back{
        display: flex;
        justify-content: flex-end;
        align-items: center;
        background-image: url('./images/bg-card-back.png');
        background-size: 100% 100%;
        background-repeat: no-repeat;
        width: 70%;
        height: 70%;
        max-width: 290px;
        max-height: 160px;
        margin-left: 20%;
        margin-bottom: 20%;
        p {
            color: #fff;
            margin-right: 40px;
        }   
    }
}

.small{
    font-size: 8px;
}

.heading{
  text-transform: uppercase;
  margin: 0;
}

.big{
  font-size: 14px;
}

.large{
  font-size: 20px;
}

.gray{
  color: #acacac;
}

form{
  margin-bottom: 50px;
}

.form{
    width: 80%;
    margin: 50px auto 0 auto;
    display: flex;
    flex-direction: column;
    &__input{
        display: flex;
        flex-direction: column;
        margin-bottom: 25px;
        text-align: left;
        &:last-child{
            margin-bottom: 0;
        }
        label{
            text-transform: uppercase;
            margin-bottom: 5px;
            white-space: nowrap;
        }
    }
    &__footer{
        display: grid;
        grid-template-columns: 1fr 1fr 2fr;
        gap: 5px;
    }
}
.input{
    width: 100%;
    outline: none;
    border: 1px solid #acacac;
    border-radius: 5px;
    padding: 10px;
    color: #acacac;
    box-sizing: border-box;
}

.submit{
    background-color: #220930;
    color: #fff;
}

.button{
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  border: none;
}

.notification{
  font-size: 12px;
  color: red;
}

.complete{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  width: 80%;
  min-height: 250px;
  margin: 0 auto;
  margin-top: 20%;
  padding-bottom: 50px;
  img {
    width: 80px;
    height: auto;
  }
  a {
    width: 100%;
  }
}

</style>

<style>
  @import url('./styles/desktop-style.css');
</style>
