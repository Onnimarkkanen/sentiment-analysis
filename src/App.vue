<template>
  <div class="app-container">
    <!-- Header-alue -->
    <header class="text-center py-4">
      <h1>Sentiment Analysis Tool</h1>
      <p>Analyze the sentiment of your text with confidence</p>
    </header>

    <!-- Lomake ja tulokset -->
    <div class="content-container">
      <!-- Lomake -->
      <form @submit.prevent="analyzeSentiment" class="analysis-form">
        <label for="inputText" class="form-label">Enter text for analysis:</label>
        <textarea
          v-model="inputText"
          id="inputText"
          rows="4"
          class="form-control"
          placeholder="Type your text here..."
        ></textarea>
        <button type="submit" class="btn-submit">Analyze</button>
      </form>

      <!-- Virheviesti -->
      <div v-if="error" class="error-message">{{ error }}</div>

      <!-- Tulosten näyttö -->
      <div v-if="result" class="result-card">
        <h2>Analysis Results</h2>
        <p :class="getSentimentClass(result.sentiment)">
          <strong>Sentiment:</strong> {{ result.sentiment }}
        </p>
        <p><strong>Confidence:</strong> {{ result.confidence }}%</p>
        <p>
          <strong>Keywords:</strong>
          <span v-if="result.keywords.length > 0">{{ result.keywords.join(", ") }}</span>
          <span v-else>None</span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      inputText: "", // Käyttäjän syöttämä teksti
      result: null,  // API-tulokset
      error: null,   // Virheviestit
    };
  },
  methods: {
    // API-kutsu sentimenttianalyysille
    async analyzeSentiment() {
      this.error = null; // Nollaa aiemmat virheet
      this.result = null; // Nollaa aiemmat tulokset

      try {
        // Lähetä POST-pyyntö Twinword API:lle
        const response = await axios.post(
          "https://twinword-twinword-bundle-v1.p.rapidapi.com/sentiment_analyze/",
          new URLSearchParams({ text: this.inputText }),
          {
            headers: {
              "Content-Type": "application/x-www-form-urlencoded",
              "x-rapidapi-host": "twinword-twinword-bundle-v1.p.rapidapi.com",
              "x-rapidapi-key": "a72046621dmshfd165df6c089ff7p161e7bjsn062b5b9cb112", // Oma API-avain
            },
          }
        );

        // Muodosta tulokset oikein
        const keywordsArray = response.data.keywords
          ? Object.keys(response.data.keywords)
          : [];

        this.result = {
          sentiment: response.data.type.charAt(0).toUpperCase() + response.data.type.slice(1),
          confidence: (response.data.score * 100).toFixed(2),
          keywords: keywordsArray, // Avainsanojen nimet
        };
      } catch (err) {
        console.error("API Error:", err);
        this.error = "Error analyzing text. Please try again.";
      }
    },

    // Palauta luokka värikoodaukselle sentimentin mukaan
    getSentimentClass(sentiment) {
      if (sentiment === "Positive") return "sentiment-positive";
      if (sentiment === "Negative") return "sentiment-negative";
      return "sentiment-neutral";
    },
  },
};
</script>

<style>
/* Yleinen tausta */
body {
  margin: 0;
  padding: 0;
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #e9f5ff, #cfe2ff);
  color: #333;
}

/* Sovelluksen pääsäiliö */
.app-container {
  max-width: 700px;
  margin: 30px auto;
  background: #ffffff;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  overflow: hidden;
}

/* Header */
header {
  background: #007bff;
  color: white;
  padding: 20px;
  text-align: center;
}

header h1 {
  margin: 0;
  font-size: 2rem;
}

header p {
  margin-top: 5px;
  font-size: 1rem;
}

/* Sisältöalue */
.content-container {
  padding: 20px;
}

/* Lomake */
.analysis-form {
  display: flex;
  flex-direction: column;
}

.analysis-form textarea {
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 15px;
  resize: none;
}

.btn-submit {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 0;
  font-size: 1.1rem;
  border-radius: 5px;
  cursor: pointer;
}

.btn-submit:hover {
  background-color: #0056b3;
}

/* Virheviesti */
.error-message {
  color: #dc3545;
  text-align: center;
  font-weight: bold;
  margin-top: 15px;
}

/* Tulosten kortti */
.result-card {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 5px;
  text-align: center;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.result-card h2 {
  margin-bottom: 15px;
}

/* Sentimentin väritys */
.sentiment-positive {
  color: #28a745;
  font-weight: bold;
}

.sentiment-negative {
  color: #dc3545;
  font-weight: bold;
}

.sentiment-neutral {
  color: #ffc107;
  font-weight: bold;
}
</style>
