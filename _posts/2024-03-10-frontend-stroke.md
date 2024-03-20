---
toc: true
layout: base
search_exclude: false
permalink: stroke
courses: {compsci: {week: 26}}
---
<body>
    <form id="strokeForm">
        <label for="name">Name:</label>
        <input type="text" id="name" name="name" required><br><br>
        <label for="gender">Gender:</label>
        <select id="gender" name="gender" required>
            <option value="Male">Male</option>
            <option value="Female">Female</option>
        </select><br><br>
        <label for="age">Age:</label>
        <input type="number" id="age" name="age" required><br><br>
        <label for="hypertension">Hypertension (0 for No, 1 for Yes):</label>
        <input type="number" id="hypertension" name="hypertension" required><br><br>
        <label for="heart_disease">Heart Disease (0 for No, 1 for Yes):</label>
        <input type="number" id="heart_disease" name="heart_disease" required><br><br>
        <label for="residence_type">Residence Type:</label>
        <select id="residence_type" name="residence_type" required>
            <option value="urban">Urban</option>
            <option value="rural">Rural</option>
        </select><br><br>
        <label for="avg_glucose_level">Average Glucose Level:</label>
        <input type="number" id="avg_glucose_level" name="avg_glucose_level" required><br><br>
        <label for="bmi">BMI:</label>
        <input type="number" id="bmi" name="bmi" required><br><br>
        <label for="smoking_status">Smoking Status:</label>
        <select id="smoking_status" name="smoking_status" required>
            <option value="never smoked">Never Smoked</option>
            <option value="smokes">Smokes/Has Smoked</option>
        </select><br><br>
        <button type="button" onclick="predictStroke()">Predict Stroke</button>
    </form>
    <div id="result"></div>
   <script>
    function predictStroke() {
        var form = document.getElementById('strokeForm');
        var name = document.getElementById('name');
        var resultDiv = document.getElementById('result');
        var formData = {
            gender: form['gender'].value,
            age: form['age'].value,
            hypertension: form['hypertension'].value,
            heart_disease: form['heart_disease'].value,
            residence_type: form['residence_type'].value,
            avg_glucose_level: form['avg_glucose_level'].value,
            bmi: form['bmi'].value,
            smoking_status: form['smoking_status'].value
        };
        fetch('http://localhost:8086/api/stroke/predict', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json'
            },
            body: JSON.stringify(formData)
        })
        .then(response => response.json())
        .then(data => {
            resultDiv.innerHTML = '<h2>Prediction Result for ' + name.value + '</h2>';
            for (var key in data) {
                resultDiv.innerHTML += '<p>' + key + ': ' + data[key] + '</p>';
            }
            var strokeProbability = parseFloat(data['stroke_prob']);
        })
        .catch(error => {
            console.error('Error:', error);
        });
    }
    </script>

</body>
