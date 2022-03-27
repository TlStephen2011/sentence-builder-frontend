<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <v-select
          :items="filteredWordTypes"
          label="Word Types"
          v-model="selectedWordType"
          @change="loadWords"
          outlined
        ></v-select>
        <v-select
          :items="filteredWords"
          label="Words"
          outlined
          v-model="selectedWord"
        ></v-select>
        <v-btn
          depressed
          elevation="6"
          @click="addWordToSentence"
        >Add Word To Sentence</v-btn>
      </v-col>
      <v-col>
        <v-textarea
          id="sentence"
          filled
          label="Sentence Viewer"
          auto-grow
          disabled
          v-model="sentence"
        ></v-textarea>
        <v-btn
          depressed
          elevation="6"
          @click="completeSentence"
        >Done</v-btn>
        <v-card
          v-for="(sentence, index) in previousSentences"
          :key="index"
          elevation="2"
          style="margin-top: 20px;"
        >
          <v-card-text><div>{{ sentence }}</div></v-card-text>
        </v-card>
      </v-col>

    </v-row>
  </v-container>
</template>

<script>
  export default {
    name: 'sentence-builder',
    async beforeMount() {
      await this.loadSentences();
      await this.loadWordTypes();
    },
    data: () => ({
      wordTypes: [],
      selectedWordType: '',
      words: [],
      selectedWord: '',
      previousSentences: [],
      sentence: '',
      position: 1,
    }),
    methods: {
      async addWordToSentence() {
        let word = null;
        for (let x in this.words) {
          if (this.words[x].word === this.selectedWord) {
            const api = 'http://localhost:3000/sentence';
            this.axios.post(api, {
              wordId: this.words[x].id,
              position: this.position
            });
            this.sentence += this.words[x].word + " ";
          }
        }
        this.position += 1;
      },
      async completeSentence() {
        const api = 'http://localhost:3000/sentence';
        await this.axios.post(api, {
              position: -1
        });
        this.sentence = '';
        this.position = 1;
        await this.loadSentences();
      },
      async loadWordTypes() {
        const api = 'http://localhost:3000/word-types';
        await this.axios.get(api).then((response) => {
          this.wordTypes = response.data;
        })
      },
      async loadWords() {
        var api = 'http://localhost:3000/words';
        var wordTypeObj = this.findWordTypeIdByWordType(this.selectedWordType);
        api += '?id=' + wordTypeObj.id;
        await this.axios.get(api).then((response) => {
          this.words = response.data;
        })
      },
      async loadSentences() {
        const api = 'http://localhost:3000/sentence';
        await this.axios.get(api).then((response) => {
          this.previousSentences = response.data;
        })
      },
      findWordTypeIdByWordType(word) {
        for (let x in this.wordTypes) {
          if (this.wordTypes[x].wordType === word)
            return this.wordTypes[x];
        }
        return null;
      }
    },
    computed: {
      filteredWords: function() {
        if (this.words.length == 0) return [];

        return this.words.reduce((a, currentValue) =>
          [...a, currentValue.word], []);
      },
      filteredWordTypes: function() {
        if (this.wordTypes.length == 0) return [];

        return this.wordTypes.reduce((a, currentValue) =>
          [...a, currentValue.wordType], []);
      }
    }
  }
</script>