<template>
  <div>
    <!--т.к здесь указано очень большое кол-во форм для заполнения, то было принято решение, сделать 3 блока с формами, где каждый из них -->
    <h2>Форма нового клиента</h2>
    <!--1ый блок-->
    <div v-if="countCreate == 0">
      <div class="input-item">
        <input type="text" v-model="client.name" placeholder="Имя" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.surname" placeholder="Фамилия" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.patronymic" placeholder="Отчество" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="tel" name="phone_number" maxlength="12" minlength="12" v-model="client.phone" onkeyup="this.value = this.value.replace (/[^0-9+]/, '')" placeholder="Телефон" @focus="client.phone = '+7'" class="input_in_form">
      </div>
      <div class="input-item">
        Дата рождения <input type="date" v-model="client.dayBith" placeholder="Дата рождения" max="21-11-2023" min="1850-01-01">
      </div>
      <div class="input-item">
        Пол: 
        <select v-model="client.gender">
          <option disabled value="">-</option>
          <option>Мужской</option>
          <option>Женский</option>
        </select>
      </div>
      <div class="input-item checkbox_style">
        Группа клиентов:  <br/>
        <input type="checkbox" id="1" value="VIP" v-model="client.groupClients">
        <label for="1">VIP</label>
        <input type="checkbox" id="2" value="Проблемные" v-model="client.groupClients">
        <label for="2">Проблемные</label>
        <input type="checkbox" id="3" value="ОМС" v-model="client.groupClients">
        <label for="3">ОМС</label>
      </div>
      <div class="input-item checkbox_style">
        Лечащий врач <br/>
        <input type="radio" id="1" value="Иванов" v-model="client.doctor">
        <label for="1">Иванов</label>
        <input type="radio" id="2" value="Захаров" v-model="client.doctor">
        <label for="2">Захаров</label>
        <input type="radio" id="3" value="Чернышева" v-model="client.doctor">
        <label for="3">Чернышева</label>
      </div>
      <div class="input-item">
        <input type="checkbox" id="1" :value="!client.sms" v-model="client.sms">
        <label for="1">Не отправлять СМС</label>
      </div>
    </div>
    <!--2ой блок, адрес-->
    <div v-if="countCreate == 1" class="form_container">
      <div class="input-item">
        <input type="text" v-model="client.address.index" placeholder="Индекс" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.address.country" placeholder="Страна" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.address.region" placeholder="Область" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.address.city" placeholder="Город*" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.address.street" placeholder="Улица" class="input_in_form">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.address.house" placeholder="Дом" class="input_in_form">
      </div>
    </div>
    <!--3ий блок, паспортные данные-->
    <div v-if="countCreate == 2">
      <div class="input-item">
        <input type="text" v-model="client.passport.series" placeholder="Серия" class="input_in_form" onkeyup="this.value = this.value.replace (/[^0-9+]/, '')">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.passport.number" placeholder="Номер" class="input_in_form" onkeyup="this.value = this.value.replace (/[^0-9+]/, '')">
      </div>
      <div class="input-item">
        <input type="text" v-model="client.passport.issued" placeholder="Кем выдан" class="input_in_form">
      </div>
      <div class="input-item">
        Дата выдачи<input type="date" v-model="client.passport.dateOfIssue" placeholder="Дата рождения" max="21-11-2023" min="1850-01-01">
      </div>
      <div class="input-item checkbox_style">
        Тип документа <br/>
        <input type="radio" id="1" value="Паспорт" v-model="client.passport.type_document">
        <label for="1">Паспорт</label> <br/>
        <input type="radio" id="2" value="Свидетельство о рождении" v-model="client.passport.type_document">
        <label for="2">Свидетельство о рождении</label> <br/>
        <input type="radio" id="3" value="Вод. удостоверение" v-model="client.passport.type_document">
        <label for="3">Вод. удостоверение</label> <br/>
      </div>
    </div>
    <button @click="countCreate--" v-if="countCreate != 0">Назад</button>
    <button @click="CheckForms()" v-if="countCreate != 2" class="button_next">Далее</button>
    <button v-if="countCreate == 2" class="button_finish" @click="CheckForms()">Сохранить</button>
    
    <div class="error_container">

        <div class="error_card" v-for="(error, index) in errorList">
          <div class="close_error" @click="CloserError(index)">&times;</div>
          {{ index+1 }}. {{ error }}
        </div>
    </div>
    <div v-for="client in clients">
        {{ client }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'MainPage',
  data () {
    return {
      client: {
        name: "", //*
        surname: "", //*
        patronymic: "",
        dayBith: "", //*
        phone: "", //11 цифр. Начинается с 7 *
        gender: "", //пол
        groupClients: [], //что-то из: VIP, Проблемные, ОМС *
        doctor: "",
        sms: false,

        address: {
          index: "",
          country: "",
          region: "",
          city: "", //*
          street: "",
          house: "" //возможно что дом будет не только с цифрой (1а, 2б и т.д)
        },

        passport: {
          type_document: "", //*
          series: "",
          number: "",
          issued: "",
          dateOfIssue: "" //*
        }
      },
      //фаза заполнения в зависимости от неё отображаем тот или иной блок и от неё зависит то, какую форму проверяем
      countCreate: 0,
      //здесь содержится текст ошибок которые появились при попытке перехода к следующему блоку
      errorList: [],
      //сохранённые клиенты
      clients: []
    }
  },
  methods: {
      //определяет проверка какого блока сейчас идёт
      CheckForms: function() {
        this.errorList.length = 0;
        switch(this.countCreate) {
          case 0:
            this.CkeckFirstForms();
            break;
          case 1:
            this.CheckAdressForm();
            break;
          case 2:
            this.CheckPassport();
            break;
        }
      },
      //Проверка все ли обязательные поля заполнены в 1ом блоке форм
      CkeckFirstForms: function() {
      if((this.client.name && this.client.surname) == "") {
        this.errorList.push("Поля Имя и Фамилия не могут быть пустыми!");
      }
      if(this.client.dayBith == "") {
        this.errorList.push("Заполните дату рождения!")
      } 
      if(this.client.phone.length != 12) {
        //12 т.к внутри храним + вначале
        this.errorList.push("Номер телефона не заполнен корректно!")
      }
      if(this.client.groupClients.length == 0) {
        this.errorList.push("Выберите группу клиентов!");
      }
      if(this.client.name != "" && this.client.surname != "" && this.client.dayBith != "" && this.client.phone.length == 12 && this.client.groupClients.length != 0) {
        this.countCreate++;
      }
    },
    //Проверка все ли обязательные поля заполнены в 2ом блоке форм
    CheckAdressForm: function() {
      if(this.client.address.city == "") {
        this.errorList.push("Город не указан!");
      } else {
        this.countCreate++;
      }
    },
    //проверка обяательных полей документов(3ий блок)
    CheckPassport: function() {
      if(this.client.passport.type_document == "") {
        this.errorList.push("Тип документа не выбран!");
      }
      if(this.client.passport.dateOfIssue == "") {
        this.errorList.push("Дата выдачи не указана!");
      }
      if(this.client.passport.type_document != "" && this.client.passport.dateOfIssue != "") {
        this.SaveNewClient()
      }
    },
    //сохранение нового клиента в массив клиентов
    SaveNewClient: function() {
      this.countCreate = 0;
      this.clients.push(this.client);
      this.client = {};
    },
    //закрытие карточки ошибки
    CloserError: function(index) {
        this.errorList.splice(index, 1);
    },
  }
}
</script>

<style scoped>
.error_container{
  position: fixed;
  top: 5%;
  right: 10%;
}
.error_card{
  margin: 3%;
  background-color: brown;
  width: 200px;
  height: 70px;
  color: white;
  text-align: left;
}
.close_error{
  text-align: right;
  margin-right: 10px;
  font-size: 110%;
}
.close_error:hover{
  cursor: pointer;
}
.input_in_form {
  margin: 1%;
  padding: 10px;
  width: 200px;
  border-radius: 2px;
}
select {
  padding: 10px;
  margin: 1%;
  border-radius: 2px;
}
option {
  font-size: 110%;
}
.button_next {
  background-color: blue;
  color: white;
}
button {
  padding: 1%;
  border-radius: 5px;
  font-size: 100%;
}
button:hover {
  cursor: pointer;
}
.button_finish {
  background-color: green;
  color: white;
}
.input-item{
  margin-top: 1%;
  margin-bottom: 1%;
}
.form_container{
  columns: 2;
}
@media (max-width: 600px) {
        .form_container {
          columns: 1;
        }
    }
</style>
