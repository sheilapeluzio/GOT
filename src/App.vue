<template>
  <div id="app">
    <div class="header">
      <div class="aoladoesquerdo">
        <img src="@/assets/Header.png" alt="" />
      </div>

      <div class="aoladodireito"></div>
    </div>
    <div class="container">
      <div class="col1">
        <div class="titlePr">
          <div class="titlePrP">FAMÍLIAS</div>
        </div>
        <div class="columnLeft">
          <div class="familia" v-for="house in houses" v-bind:key="house.slug">
            <button
              class="family"
              :class="{ teste: selectedHouseCss(house) }"
              @click="
                selectedHouse = house;
                showDetail = false;
              "
            >
              <div>
                <img
                  class="imgBrazao"
                  :src="require(`@/assets/${house.slug.toLowerCase()}.png`)"
                  alt="house"
                />
              </div>
              <div class="nome">House {{ house.slug }}</div>
            </button>
          </div>
        </div>
      </div>

      <div class="col1" v-if="showCharacterColumn">
        <div class="titlePr">
          <div class="titlePrP">MEMBROS</div>
        </div>
        <div class="columnLeft">
          <div class="fixa">
            <div
              class="selectedCharacters"
              v-for="char in selectedCharacters"
              v-bind:key="char.id"
            >
              <button
                class="family"
                :class="{ teste: selectedCharacterCss(char) }"
                @click="
                  selectedCharDetails = char;
                  showDetail = true;
                "
              >
                <div>
                  <img class="imagemPersona" v-bind:src="char.imageUrl" />
                </div>
                <div class="nome">{{ char.fullName }}</div>
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="detalhes" v-if="!isCharDetailEmpty && showDetail">
        <div class="titleDetail">DETAHES DA PERSONAGEM</div>

        <div class="personagem">
          <div class="detail">
            <img class="imagem" v-bind:src="selectedCharDetails.imageUrl" />
            <div class="nameContainer">
              <img src="@/assets/floral.png" alt="" />
              <div class="namePerson">{{ selectedCharDetails.fullName }}</div>
            </div>
            <p class="titlePr">Título</p>
            <div class="title">{{ selectedCharDetails.title }}</div>
            <p class="titleC">Citação</p>
            <div class="citacaoContainer">
              <img src="@/assets/citacao.png" alt="" />
              <div class="citacao">{{ selectedCharDetails.sentence }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data: function () {
    return {
      houses: [],
      characters: [],
      details: [],
      selectedHouse: {},
      selectedCharacters: [],
      selecterChar: {},
      selectedCharDetails: [],
      showDetail: false,
    };
  },
  components: {},
  methods: {
    selectedHouseCss: function (house) {
      console.log("HouseCss: ", house);

      if (house.slug === this.selectedHouse.slug) return true;
      return false;
    },
    selectedCharacterCss: function (char) {
      console.log("ID: ", char);
      console.log("IDselecionado: ", this.selectedCharDetails.id);
      if (char.id === this.selectedCharDetails.id) return true;
      return false;
    },
  },
  mounted() {
    axios
      .get("https://game-of-thrones-quotes.herokuapp.com/v1/houses")
      .then((response) => {
        this.houses = response.data;
        console.log("Houses:", this.houses);
      });
    axios.get("https://thronesapi.com/api/v2/Characters").then((response) => {
      this.characters = response.data;
      this.teste = this.characters.map(function (item) {
        return item.family;
      });
    });
    axios
      .get("https://game-of-thrones-quotes.herokuapp.com/v1/random/5")
      .then((response) => {
        this.details = response.data;
      });
  },

  computed: {
    isCharDetailEmpty: function () {
      const a =
        this.selectedCharacters.firstName ===
        this.selectedCharDetails.firstName;
      return a;
    },
    showCharacterColumn: function () {
      return !(Object.keys(this.selectedHouse).length === 0);
    },
  },
  watch: {
    selectedHouse: function (nval) {
      console.log("NVAL:", nval);
      this.selectedCharacters = this.characters.filter((char) => {
        if (
          char.lastName
            .toLocaleLowerCase()
            .includes(nval.slug.toLocaleLowerCase()) ||
          char.family
            .toLocaleLowerCase()
            .includes(nval.slug.toLocaleLowerCase()) ||
          char.fullName
            .toLocaleLowerCase()
            .includes(nval.slug.toLocaleLowerCase())
        ) {
          console.log("Personagens: ", this.selectedCharacters);
          return char;
        }
      });
    },
    selectedCharDetails: function (n) {
      console.log("chardetails", n);
      const found = this.details.find((d) => {
        if (
          this.selectedCharDetails.fullName
            .toLocaleLowerCase()
            .includes(d.character.name.toLocaleLowerCase()) ||
          this.selectedCharDetails.firstName
            .toLocaleLowerCase()
            .includes(d.character.name.toLocaleLowerCase()) ||
          this.selectedCharDetails.lastName
            .toLocaleLowerCase()
            .includes(d.character.name.toLocaleLowerCase())
        ) {
          console.log("d:", d);

          this.selectedCharDetails.sentence = d.sentence;
          return found;
        }
      });
      if (this.selectedCharDetails.sentence === undefined)
        this.selectedCharDetails.sentence = "Sem citação";
    },
  },
};
</script>


<style>
#app {
  display: flex;
  flex-direction: column;
  padding-left: 5rem;
  /* background-color: green; */
}
.header {
  display: flex;
  flex-direction: row;
  margin: 3.375rem 0px 5.513rem 0px;
}
.aoladoesquerdo {
  display: flex;
  flex: 1;
}

.aoladodireito {
  display: flex;
  flex: 1;
}
.container {
  background-color: rgba(0, 0, 0, 0);
  display: flex;
  flex-direction: row;
}
.col1 {
  display: flex;
  flex-direction: column;
  /* margin: 0px 70px 0px 10px; */
  background-color: rgba(0, 0, 0, 0);
}

.columnLeft {
  display: flex;
  flex-direction: column;
  margin: 0px 70px 0px 0px;
  padding-top: 8px;
  background-color: #151515;
}
.fixa {
  display: flex;
  flex-direction: column;
  width: 360px;
  height: 958px;
  background-color: #151515;
}
.familia {
  list-style: none;
  display: flex;
  background-color: #151515;
}
.selectedCharacters {
  list-style: none;
  display: flex;
  background-color: #151515;
}
.titlePr {
  display: flex;
  font-family: Montserrat;
  font-style: normal;
  font-weight: 600;
  font-size: 11px;
  line-height: 13px;
  letter-spacing: 0.25em;
  margin: 0px 0px 10.5px 0px;
  background-color: rgb(0, 0, 0, 0);
  color: #444444;
}
.titlePrP {
  padding-bottom: 10px;
  margin: 0px 0px 10px 0px;
}
.imagemPersona {
  width: 50px !important;
  height: 50px !important;
  border-radius: 50%;
  margin: 0px 0px 0px 25px;
}
.detalhes {
  background-color: #151515;
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 400px;
  height: 1434px;
  left: 1040px;
  top: 0px;
}
.titleDetail {
  display: flex;
  font-family: Montserrat;
  font-style: normal;
  font-weight: 600;
  font-size: 11px;
  line-height: 13px;
  letter-spacing: 0.25em;
  background-color: rgb(0, 0, 0, 0);
  color: #444444;
  margin: 71px 87px 72px 87px;
}
.family {
  width: 320px;
  height: 80px;
  background-color: #222222;
  margin: 7.5px 20px 7.5px 20px;
  cursor: url(./assets/mao.png), auto;
  border: 0;
  display: flex;
  flex-direction: row;
  align-items: center;
  color: #FFFFFF;
  
}
.nome {
  margin: 0px 0px 0px 15px;
  font-family: Montserrat;
  font-style: normal;
  font-weight: 600;
  font-size: 14px;
  line-height: 17px;
  text-transform: capitalize;
  
}
.teste {
  background-color: #DEDEDE;
  color: #414141;
}
.imgBrazao {
  margin: 0px 0px 0px 25px;
}
.imagem {
  width: 193px !important;
  height: 261px !important;
  border: 2px solid #ffffff;
  top: 156px;
  margin: 0px 0px 22px 0px;
}
.nameContainer {
  position: relative;
  text-align: center;
  width: 250px;
  margin: 20px 35px 5px 35px;
}
.personagem {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.detail {
  display: flex;
  flex-direction: column;
  background-color: #151515;
  width: 300px;
  height: 700px;
  color: azure;
  font-weight: bold;
  border: 5;
  align-items: center;
  justify-items: center;
}
.citacaoContainer {
  display: flex;
  align-items: flex-start;
  flex-direction: column;
  margin: 5px 35px 5px 35px;
}
.citacao {
  display: flex;
  align-items: center;
  font-family: Garamond;
  font-style: italic;
  font-weight: normal;
  font-size: 12px;
  line-height: 22px;
  text-align: center;
}
.namePerson {
  font-family: Georgia, "Times New Roman", Times, serif;
  font-style: normal;
  font-weight: normal;
  font-size: 18px;
  line-height: 25px;
  line-height: 25px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.titleC {
  font-family: Montserrat;
  font-style: normal;
  font-weight: 600;
  font-size: 11px;
  line-height: 13px;
  text-align: center;
  letter-spacing: 0.25em;
  color: #444444;
}
.title {
  font-family: Montserrat;
  font-style: normal;
  font-weight: normal;
  font-size: 14px;
  line-height: 17px;
}
</style>
