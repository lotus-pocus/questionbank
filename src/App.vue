<template>
  <div id="app">
    <h1>Create Quiz Question</h1>

    <div class="form-group">
      <label for="category">Select Category:</label>
      <select v-model="newQuestion.category" id="category">
        <option v-for="category in categories" :key="category" :value="category">
          {{ category }}
        </option>
      </select>
    </div>

    <div class="form-group">
      <label for="subCategory">Select Sub-Category:</label>
      <select v-model="newQuestion.subCategory" id="subCategory">
        <option v-for="subCategory in availableSubCategories" :key="subCategory" :value="subCategory">
          {{ subCategory }}
        </option>
      </select>
    </div>

    <div class="form-group">
      <label for="question">Question:</label>
      <input
        type="text"
        id="question"
        v-model="newQuestion.text"
        maxlength="100"
        placeholder="Enter your question"
      />
    </div>

    <div class="form-group" v-for="(answer, index) in newQuestion.answers" :key="index">
      <label :for="'answer' + index">Answer {{ String.fromCharCode(65 + index) }}:</label>
      <input
        type="text"
        :id="'answer' + index"
        v-model="newQuestion.answers[index]"
        maxlength="60"
        placeholder="Enter answer"
      />
      <input
        type="radio"
        :value="index"
        v-model="newQuestion.correctAnswerIndex"
        :id="'correctAnswer' + index"
      />
      <label :for="'correctAnswer' + index">Correct</label>
    </div>

    <button @click="addQuestion">Submit</button>

    <h2>All Questions</h2>
    <div v-if="questions.length === 0">
      <p>No questions created yet.</p>
    </div>
    <div v-for="(question, index) in questions" :key="index" class="question">
      <h3>{{ question.category }} - {{ question.subCategory }}: {{ question.text }}</h3>
      <ul>
        <li v-for="(answer, i) in question.answers" :key="i">
          {{ String.fromCharCode(65 + i) }}: {{ answer }}
          <span v-if="i === question.correctAnswerIndex"> (Correct)</span>
        </li>
      </ul>
      <button @click="deleteQuestion(index)">Delete</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      categories: ['Arts', 'Food', 'General', 'Geography', 'History', 'Logic', 'Mythology', 'Nature', 'Occasion', 'Online-Media', 'Politics', 'Science', 'Sports'],
      subCategories: {
        Arts: ['Movies & TV', 'Art', 'Beauty', 'Boardgame', 'Dance', 'Fashion', 'Video Games', 'Music', 'Literature', 'Theatre'],
        Food: ['Europe', 'North America', 'South America', 'Asia', 'Africa', 'Oceania', 'Drink', 'Snack'],
        General: ['Europe', 'Asia', 'North America', 'South America', 'Oceania', 'Africa', 'Languages', 'Culture', 'Technology', 'Horoscopes', 'Famous People', 'Seasonal', 'All'],
        Geography: ['Europe', 'Asia', 'North America', 'South America', 'Oceania', 'Africa', 'Antartica', 'Eurasia', 'Oceans', 'Mountains', 'Rivers', 'Space', 'Misc'],
        History: ['Europe', 'Asia', 'North America', 'South America', 'Oceania', 'Africa','Dinosaurs', 'Events', 'Famous People'],
        Logic: ['All'],
        Mythology: ['Greek', 'Norse', 'Roman', 'Egyptian', 'Chinese', 'Hindu', 'Japanese'],
        Nature: ['Animals', 'Plants', 'Entomology', 'Environment'],
        Occasion: ['Christmas', 'Easter', 'Halloween', 'Thanksgiving'],
        'Online-Media': ['Social Media', 'Content Creators', 'Podcasts'], // Fixed key by removing leading space
        Politics: ['Europe', 'Asia', 'North America', 'South America', 'Oceania', 'Africa', 'Famous People'],
        Science: ['Physics', 'Space', 'Chemistry', 'General', 'Biology', 'Famous People'],
        Sports: ['Water', 'Winter', 'Football', 'Basketball', 'Tennis', 'Boxing', 'Bowling', 'Polo', 'Events', 'Cricket', 'Golf', 'Baseball', 'Esports', 'Volleyball', 'American Football', 'Darts', 'Athletics', 'Rugby', 'Badminton', 'Gymnastics', 'Swimming', 'Famous People']
      },
      newQuestion: {
        category: 'Arts',
        subCategory: '',
        text: '',
        answers: ['', '', '', ''],
        correctAnswerIndex: null,
      },
      questions: [],
    };
  },
  computed: {
    availableSubCategories() {
      console.log('Sub-categories for', this.newQuestion.category, ':', this.subCategories[this.newQuestion.category]);
      return this.subCategories[this.newQuestion.category] || [];
    }
  },
  watch: {
    'newQuestion.category': function () {
      this.newQuestion.subCategory = this.availableSubCategories[0] || '';
    }
  },
  methods: {
    addQuestion() {
      if (
        this.newQuestion.text &&
        this.newQuestion.answers.every((answer) => answer.trim() !== '') &&  // Ensure no empty answers
        this.newQuestion.correctAnswerIndex !== null &&
        this.newQuestion.subCategory
      ) {
        this.questions.push({ ...this.newQuestion });
        this.saveQuestionsToLocalStorage();
        this.resetForm();
      } else {
        alert('Please fill out the question, all answers, select the correct answer, and choose a sub-category.');
      }
    },
    resetForm() {
      this.newQuestion = {
        category: this.categories[0],
        subCategory: this.availableSubCategories[0] || '',
        text: '',
        answers: ['', '', '', ''],
        correctAnswerIndex: null,
      };
    },
    saveQuestionsToLocalStorage() {
      localStorage.setItem('questions', JSON.stringify(this.questions));
    },
    loadQuestionsFromLocalStorage() {
      const savedQuestions = localStorage.getItem('questions');
      if (savedQuestions) {
        this.questions = JSON.parse(savedQuestions);
      }
    },
    deleteQuestion(index) {
      this.questions.splice(index, 1);
      this.saveQuestionsToLocalStorage();
    },
  },
  created() {
    this.loadQuestionsFromLocalStorage();
    this.newQuestion.subCategory = this.availableSubCategories[0] || '';  // Set the initial sub-category
  }
};
</script>

<style scoped>
#app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1, h2 {
  text-align: center;
}

.form-group {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

input[type="text"],
select {
  flex: 1;
  width: 100%;
  height: 40px;
  padding: 10px;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

input[type="radio"] {
  transform: scale(1.5);
  margin-left: 10px;
  margin-right: 10px;
}

button {
  display: block;
  width: 100%;
  padding: 15px;
  background-color: #4CAF50;
  color: white;
  border: none;
  font-size: 18px;
  cursor: pointer;
  border-radius: 5px;
}

button:hover {
  background-color: #45a049;
}

.question {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}

ul {
  list-style-type: none;
  padding-left: 0;
}

li {
  margin-bottom: 5px;
}
</style>
