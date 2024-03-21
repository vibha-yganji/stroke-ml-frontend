---
toc: true
layout: base
search_exclude: false
permalink: stroke
---
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Stroke Prediction Form</title>
</head>
<body>
    <form id="strokeForm">
        <p><strong>Important Note: </strong> Some aspects of these predictions are not accurate due to the ages in the dataset not encompassing a representative amount of 70+ and those below 20 years old. There are also no extreme blood glucose levels (> 120 mg/dL) or BMI's (>86) recorded in this dataset as well which will cause any inputs of extreme values to give incorrect predictions as well.</p> <br> <br>
        <label for="name">Name:</label>
        <input type="text" id="name" name="name" required><br><br>
        <label for="gender">Gender:</label>
        <select id="gender" name="gender" required>
            <option value="Male">Male</option>
            <option value="Female">Female</option>
            <option value="Other">Other</option>
        </select><br><br>
        <label for="age">Age:</label>
        <input type="number" id="age" name="age" required><br><br>
        <label for="hypertension">Hypertension:</label>
        <select id="hypertension" name="hypertension">
            <option value="0">No hypertension</option>
            <option value="1">Has hypertension</option>
        </select><br><br>
        <label for="heart_disease">Heart Disease:</label>
        <select id="heart_disease" name="heart_disease">
            <option value="0">No heart disease</option>
            <option value="1">Has heart disease</option>
        </select><br><br>
        <label for="Residence_type">Residence Type:</label>
        <select id="Residence_type" name="Residence_type" required>
            <option value="Urban">Urban</option>
            <option value="Rural">Rural</option>
        </select><br><br>
        <label for="avg_glucose_level">Average Glucose Level (highest possible can be 290 mg/dL):</label>
        <input type="number" id="avg_glucose_level" name="avg_glucose_level" required><br><br>
        <label for="bmi">BMI (range can be from 10 up to 180):</label>
        <input type="number" id="bmi" name="bmi" required><br><br>
        <label for="smoking_status">Smoking Status:</label>
        <select id="smoking_status" name="smoking_status" required>
            <option value="never smoked">Never Smoked</option>
            <option value="smokes">Smokes/Has Smoked</option>
        </select><br><br>
        <button type="button" onclick="predictStroke()">Predict Probability of Stroke</button>
    </form>
    <div id="result"></div>
    <script>
        function predictStroke() {
            var form = document.getElementById('strokeForm');
            var name = document.getElementById('name');
            var resultDiv = document.getElementById('result');
            // Check if any required fields are empty
            var requiredFields = form.querySelectorAll('input[required], select[required]');
            var isEmpty = Array.from(requiredFields).some(field => !field.value.trim());
            if (isEmpty) {
                resultDiv.innerHTML = '<p>Please fill out all required fields.</p>';
                return;
            }
            var formData = {
                gender: form['gender'].value,
                age: form['age'].value,
                hypertension: form['hypertension'].value,
                heart_disease: form['heart_disease'].value,
                Residence_type: form['Residence_type'].value,
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
                if (strokeProbability < 30) {
                    resultDiv.innerHTML += '<p>You are healthy and not in danger of a stroke! </p>';
                } else {
                    resultDiv.innerHTML += '<p> 💀 You are in danger of a stroke. 💀 Be sure to implement a healthy lifestyle to keep yourself far from having to face this life-threatening event! Here is a <a href="https://www.cdc.gov/stroke/prevention.htm">link</a> for more information about how to prevent a stroke. </p>';
                }
            })
            .catch(error => {
                console.error('Error:', error);
            });
        }
    </script>
</body>
</html>
