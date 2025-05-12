# Survey Form Builder (ReactJS)
## Date:

## AIM
To create a Survey Form Builder using ReactJS..

## ALGORITHM
### STEP 1 Initialize States
mode ← 'build' (default mode)

questions ← [] (holds all survey questions)

currentQuestion ← { text: '', type: 'text', options: '' } (holds input while building)

editingIndex ← null (tracks which question is being edited)

responses ← {} (user responses to survey)

submitted ← false (indicates whether survey was submitted)

### STEP 2 Switch Modes
Toggle mode between 'build' and 'fill' when the toggle button is clicked.

Reset submitted to false when entering Fill Mode.

### STEP 3 Build Mode Logic
#### a. Add/Update Question
If currentQuestion.text is not empty:

If type is "radio" or "checkbox", split currentQuestion.options by commas → optionsArray.

Construct a questionObject:

If editingIndex is not null, replace question at that index.

Else, push questionObject into questions.

Reset currentQuestion and editingIndex.

#### b. Edit Question
Set currentQuestion to the selected question’s data.

Set editingIndex to the question’s index.

#### c. Delete Question
Remove the selected question from questions.

### STEP 4 Fill Mode Logic
#### a. Render Form
For each question in questions:

If type is "text", render a text input.

If type is "radio", render radio buttons for each option.

If type is "checkbox", render checkboxes.

#### b. Capture Responses
On input change:

For "text" and "radio", update responses[index] = value.

For "checkbox":

If option exists in responses[index], remove it.

Else, add it to the array.

### STEP 5 Submit Form
When user clicks "Submit":

Set submitted = true.

Display a summary using the questions and responses.

### STEP 6 Display Summary
Iterate over questions.

Display the response from responses[i] for each question:

If it's an array, join it with commas.

If empty, show "No response".


## PROGRAM

## App.css
```
body {
  margin: 0;
  padding: 0;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #e3f2fd 0%, #f1f8ff 100%);
  color: #333;
}

h1 {
  text-align: center;
  color: #2f7ebc;
  margin-bottom: 20px;
}

h2 {
  color: #4a6ee0;
  margin-top: 20px;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  background: #d7ecf7;
  padding: 30px;
  border-radius: 16px;
  box-shadow: 0 4px 25px rgba(0, 0, 0, 0.1);
}

button {
  background-color: #4a90e2;
  color: white;
  padding: 12px 22px;
  border: none;
  border-radius: 8px;
  margin: 10px 5px;
  cursor: pointer;
  font-weight: 500;
  transition: background-color 0.3s, transform 0.2s;
}

button:hover {
  background-color: #357abd;
  transform: scale(1.05);
}

input[type="text"],
select {
  padding: 10px;
  margin: 6px 8px 14px 0;
  border: 1px solid #bbb;
  border-radius: 8px;
  width: 300px;
  font-size: 15px;
  transition: border-color 0.3s;
}

input[type="text"]:focus,
select:focus {
  border-color: #4a90e2;
  outline: none;
}

input[type="radio"],
input[type="checkbox"] {
  margin-right: 6px;
}

label {
  display: inline-block;
  margin-right: 12px;
  margin-top: 6px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  background: #f0f8ff;
  padding: 12px 16px;
  margin-bottom: 10px;
  border-radius: 10px;
  border-left: 5px solid #4ac4e2;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
}

form {
  margin-top: 25px;
}

hr {
  margin: 25px 0;
  border: none;
  border-top: 2px solid #c1d8ef;
}

```
## App.js
```
APP.JS
import React, { useState } from "react";
import "./App.css";

function App() {
  const [mode, setMode] = useState("build"); // 'build' or 'fill'
  const [questions, setQuestions] = useState([]);
  const [newQuestion, setNewQuestion] = useState({
    text: "",
    type: "text",
    options: "",
  });
  const [responses, setResponses] = useState({});
  const [submitted, setSubmitted] = useState(false);

  const handleAddQuestion = () => {
    if (!newQuestion.text) return;
    const question = {
      id: Date.now(),
      text: newQuestion.text,
      type: newQuestion.type,
      options:
        newQuestion.type !== "text"
          ? newQuestion.options.split(",").map((o) => o.trim())
          : [],
    };
    setQuestions([...questions, question]);
    setNewQuestion({ text: "", type: "text", options: "" });
  };

  const handleDelete = (id) => {
    setQuestions(questions.filter((q) => q.id !== id));
  };

  const handleResponseChange = (id, value) => {
    setResponses((prev) => ({ ...prev, [id]: value }));
  };

  const handleCheckboxChange = (id, value) => {
    const current = responses[id] || [];
    if (current.includes(value)) {
      setResponses((prev) => ({
        ...prev,
        [id]: current.filter((v) => v !== value),
      }));
    } else {
      setResponses((prev) => ({ ...prev, [id]: [...current, value] }));
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    setSubmitted(true);
  };

  return (
    <div style={{ fontFamily: "Arial", padding: "20px" }}>
      <h1> SURVEY FORM BUILDER</h1>

      <button
        onClick={() => {
          setMode(mode === "build" ? "fill" : "build");
          setSubmitted(false);
        }}
      >
        Switch to {mode === "build" ? "Fill Mode" : "Build Mode"}
      </button>

      <hr />

      {mode === "build" ? (
        <>
          <h2>Build Mode</h2>
          <input
            type="text"
            placeholder="Enter question"
            value={newQuestion.text}
            onChange={(e) =>
              setNewQuestion({ ...newQuestion, text: e.target.value })
            }
          />
          <select
            value={newQuestion.type}
            onChange={(e) =>
              setNewQuestion({ ...newQuestion, type: e.target.value })
            }
          >
            <option value="text">Text</option>
            <option value="radio">Radio</option>
            <option value="checkbox">Checkbox</option>
          </select>
          {(newQuestion.type === "radio" ||
            newQuestion.type === "checkbox") && (
            <input
              type="text"
              placeholder="Options (comma separated)"
              value={newQuestion.options}
              onChange={(e) =>
                setNewQuestion({ ...newQuestion, options: e.target.value })
              }
            />
          )}
          <button onClick={handleAddQuestion}>Add Question</button>

          <ul>
            {questions.map((q) => (
              <li key={q.id}>
                <strong>{q.text}</strong> ({q.type})
                {q.options.length > 0 && (
                  <ul>
                    {q.options.map((opt, idx) => (
                      <li key={idx}>{opt}</li>
                    ))}
                  </ul>
                )}
                <button onClick={() => handleDelete(q.id)}> Delete</button>
              </li>
            ))}
          </ul>
        </>
      ) : (
        <>
          <h2>Fill Mode</h2>
          {questions.length === 0 ? (
            <p>No questions added. Switch to Build Mode to add some.</p>
          ) : (
            <form onSubmit={handleSubmit}>
              {questions.map((q) => (
                <div key={q.id} style={{ marginBottom: "15px" }}>
                  <label>
                    <strong>{q.text}</strong>
                  </label>
                  <br />
                  {q.type === "text" && (
                    <input
                      type="text"
                      value={responses[q.id] || ""}
                      onChange={(e) =>
                        handleResponseChange(q.id, e.target.value)
                      }
                    />
                  )}
                  {q.type === "radio" &&
                    q.options.map((opt, idx) => (
                      <label key={idx} style={{ marginRight: "10px" }}>
                        <input
                          type="radio"
                          name={`radio-${q.id}`}
                          value={opt}
                          checked={responses[q.id] === opt}
                          onChange={(e) =>
                            handleResponseChange(q.id, e.target.value)
                          }
                        />
                        {opt}
                      </label>
                    ))}
                  {q.type === "checkbox" &&
                    q.options.map((opt, idx) => (
                      <label key={idx} style={{ marginRight: "10px" }}>
                        <input
                          type="checkbox"
                          value={opt}
                          checked={(responses[q.id] || []).includes(opt)}
                          onChange={() => handleCheckboxChange(q.id, opt)}
                        />
                        {opt}
                      </label>
                    ))}
                </div>
              ))}
              <button type="submit">Submit</button>
            </form>
          )}

          {submitted && (
            <div>
              <h3>Survey Responses</h3>
              <ul>
                {questions.map((q) => (
                  <li key={q.id}>
                    <strong>{q.text}</strong>:{" "}
                    {Array.isArray(responses[q.id])
                      ? responses[q.id].join(", ")
                      : responses[q.id]}
                  </li>
                ))}
              </ul>
            </div>
          )}
        </>
      )}
    </div>
  );
}

export default App;
```
## OUTPUT


## RESULT
The program for creating Survey Form Builder using ReactJS is executed successfully.
