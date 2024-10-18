# CODEQUEST.24
Intelligent Answer Script Evalution System

 /iaes
|-- /static
|   |-- style.css
|-- /templates
|   |-- index.html
|-- app.py
|-- requirements.txt


 Flask
Flask-Cors
nltk
scikit-learn
numpy
pandas
textblob
pygments


 from flask import Flask, request, jsonify
from flask_cors import CORS
from textblob import TextBlob
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

app = Flask(__name__)
CORS(app)

# Sample correct answers for evaluation
correct_answers = {
    'Q1': 'The capital of France is Paris.',
    'Q2': 'The process of photosynthesis occurs in plants.'
}

# Sample text corpus for quality evaluation
text_corpus = [
    "Paris is the capital city of France.",
    "Photosynthesis is a process used by plants to convert light into energy.",
    # Add more reference texts as needed
]

@app.route('/')
def home():
    return app.send_static_file('index.html')

@app.route('/evaluate', methods=['POST'])
def evaluate():
    data = request.json
    responses = data['responses']
    results = {}

    for question, response in responses.items():
        accuracy = evaluate_accuracy(question, response)
        plagiarism = check_plagiarism(response)
        quality = evaluate_quality(response)

        results[question] = {
            'accuracy': accuracy,
            'plagiarism': plagiarism,
            'quality': quality
        }

    return jsonify(results)

def evaluate_accuracy(question, response):
    correct_answer = correct_answers.get(question, "")
    return response.strip().lower() == correct_answer.strip().lower()

def check_plagiarism(response):
    # Simple plagiarism check (placeholder implementation)
    vectorizer = TfidfVectorizer().fit_transform([response] + text_corpus)
    vectors = vectorizer.toarray()
    cosine_matrix = cosine_similarity(vectors)
    similarity = np.max(cosine_matrix[0][1:])  # Get the highest similarity score
    return similarity < 0.30  # Return True if similarity is less than 30%

def evaluate_quality(response):
    blob = TextBlob(response)
    grammar_score = len([sent for sent in blob.sentences if sent.correct()]) / len(blob.sentences)
    coherence_score = 1.0  # Placeholder; implement a real coherence analysis
    return {'grammar_score': grammar_score, 'coherence_score': coherence_score}

if __name__ == '__main__':
    app.run(debug=True)



    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="static/style.css">
    <title>IASES</title>
</head>
<body>
    <h1>Intelligent Answer Script Evaluation System</h1>
    <form id="evaluation-form">
        <label for="responses">Enter your responses (JSON format):</label><br>
        <textarea id="responses" rows="10" cols="50"></textarea><br>
        <button type="submit">Submit</button>
    </form>
    <div id="results"></div>

    <script>
        document.getElementById('evaluation-form').addEventListener('submit', function(event) {
            event.preventDefault();
            const responses = JSON.parse(document.getElementById('responses').value);
            fetch('http://localhost:5000/evaluate', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ responses })
            })
            .then(response => response.json())
            .then(data => {
                document.getElementById('results').innerText = JSON.stringify(data, null, 2);
            });
        });
    </script>
</body>
</html>


body {
    font-family: Arial, sans-serif;
    margin: 20px;
}

h1 {
    color: #333;
}

label {
    font-weight: bold;
}

textarea {
    width: 100%;
}

button {
    margin-top: 10px;
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}

#results {
    margin-top: 20px;
    white-space: pre-wrap; /* To preserve whitespace formatting */
}


pip install -r requirements.txt


python app.py

{
    "Q1": "Your answer for question 1",
    "Q2": "Your answer for question 2"
}



### 4. Push to GitHub

After you’ve added the code and structured your project:

```bash
git add .
git commit -m "Initial commit of IASES"
git push origin main


