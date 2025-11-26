# Ex06 BMI Calculator
## Date:

## AIM
To create a BMI calculator using React Router 

## ALGORITHM
### STEP 1 State Initialization
Manage the current page (Home or Calculator) using React Router.

### STEP 2 User Input
Accept weight and height inputs from the user.

### STEP 3 BMI Calculation
Calculate the BMI based on user input.

### STEP 4 Categorization
Classify the BMI result into categories (Underweight, Normal weight, Overweight, Obesity).

### STEP 5 Navigation
Navigate between pages using React Router.

## PROGRAM
```
import React, { useState, useEffect } from "react";

export default function BMICalculator() {
  const [units, setUnits] = useState("metric"); // metric or imperial
  const [weight, setWeight] = useState(70); // kg or lb
  const [height, setHeight] = useState(170); // cm or inches
  const [bmi, setBmi] = useState(null);
  const [category, setCategory] = useState("");
  const [error, setError] = useState("");

  useEffect(() => {
    calculateBMI();
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [weight, height, units]);

  function formatNumber(n) {
    return typeof n === "number" ? n.toFixed(1) : "";
  }

  function validateInputs() {
    setError("");
    if (!weight || weight <= 0) {
      setError("Please enter a valid weight greater than 0.");
      return false;
    }
    if (!height || height <= 0) {
      setError("Please enter a valid height greater than 0.");
      return false;
    }
    return true;
  }

  function calculateBMI() {
    if (!validateInputs()) {
      setBmi(null);
      setCategory("");
      return;
    }

    let bmiValue = 0;
    if (units === "metric") {
      // weight (kg) / (height (m))^2
      const hMeters = height / 100;
      bmiValue = weight / (hMeters * hMeters);
    } else {
      // imperial: (weight (lb) / (height (in))^2) * 703
      bmiValue = (weight / (height * height)) * 703;
    }

    setBmi(bmiValue);
    setCategory(bmiCategory(bmiValue));
  }

  function bmiCategory(val) {
    if (!isFinite(val)) return "";
    if (val < 18.5) return "Underweight";
    if (val < 25) return "Normal weight";
    if (val < 30) return "Overweight";
    return "Obesity";
  }

  function handleReset() {
    setWeight(units === "metric" ? 70 : 154); // 70kg ≈ 154lb
    setHeight(units === "metric" ? 170 : 67); // 170cm ≈ 67in
    setError("");
  }

  function toggleUnits(newUnits) {
    if (newUnits === units) return;
    // Convert values when switching units
    if (newUnits === "imperial") {
      // kg -> lb (1 kg = 2.20462 lb), cm -> in (1 cm = 0.393701 in)
      setWeight((w) => Math.round(w * 2.20462));
      setHeight((h) => Math.round(h * 0.393701));
    } else {
      // lb -> kg, in -> cm
      setWeight((w) => Math.round(w / 2.20462));
      setHeight((h) => Math.round(h / 0.393701));
    }
    setUnits(newUnits);
  }

  return (
    <div className="max-w-lg mx-auto p-6 bg-white/80 dark:bg-slate-900/60 rounded-2xl shadow-lg mt-8">
      <h1 className="text-2xl font-semibold mb-4 text-center">BMI Calculator</h1>

      <div className="flex gap-2 justify-center mb-4">
        <button
          className={`px-4 py-2 rounded-full border ${units === "metric" ? "bg-indigo-600 text-white" : "bg-transparent"}`}
          onClick={() => toggleUnits("metric")}
        >
          Metric (kg, cm)
        </button>
        <button
          className={`px-4 py-2 rounded-full border ${units === "imperial" ? "bg-indigo-600 text-white" : "bg-transparent"}`}
          onClick={() => toggleUnits("imperial")}
        >
          Imperial (lb, in)
        </button>
      </div>

      <div className="space-y-4">
        <label className="block">
          <span className="text-sm font-medium">Weight ({units === "metric" ? "kg" : "lb"})</span>
          <input
            type="number"
            inputMode="decimal"
            min="0"
            step="any"
            value={weight}
            onChange={(e) => setWeight(Number(e.target.value))}
            className="mt-1 w-full rounded-md border px-3 py-2 focus:outline-none focus:ring-2 focus:ring-indigo-400"
          />
        </label>

        <label className="block">
          <span className="text-sm font-medium">Height ({units === "metric" ? "cm" : "in"})</span>
          <input
            type="number"
            inputMode="decimal"
            min="0"
            step="any"
            value={height}
            onChange={(e) => setHeight(Number(e.target.value))}
            className="mt-1 w-full rounded-md border px-3 py-2 focus:outline-none focus:ring-2 focus:ring-indigo-400"
          />
        </label>

        {error && <p className="text-sm text-red-600">{error}</p>}

        <div className="flex items-center justify-between gap-4">
          <button
            onClick={calculateBMI}
            className="flex-1 px-4 py-2 rounded-md bg-green-600 text-white font-medium shadow-sm hover:bg-green-700"
          >
            Calculate
          </button>

          <button
            onClick={handleReset}
            className="px-4 py-2 rounded-md border hover:bg-slate-100"
          >
            Reset
          </button>
        </div>

        <div className="mt-4 p-4 rounded-lg border bg-gradient-to-br from-white to-slate-50 dark:from-slate-800 dark:to-slate-900">
          <h2 className="text-lg font-semibold">Result</h2>

          {bmi ? (
            <div className="mt-3">
              <p className="text-3xl font-bold">{formatNumber(bmi)}</p>
              <p className="mt-1 text-sm text-gray-600 dark:text-gray-300">{category}</p>

              <div className="mt-3 text-sm leading-6 text-gray-700 dark:text-gray-300">
                {bmi < 18.5 && <p>You are underweight. Consider a balanced diet and consult a healthcare professional if needed.</p>}
                {bmi >= 18.5 && bmi < 25 && <p>Your weight is within the normal range. Maintain a healthy lifestyle to keep it steady.</p>}
                {bmi >= 25 && bmi < 30 && <p>You are overweight. Increasing physical activity and reviewing diet can help.</p>}
                {bmi >= 30 && <p>Your BMI falls in the obesity range. It's recommended to consult a healthcare professional for personalized advice.</p>}
              </div>

              <div className="mt-3">
                <p className="text-xs text-gray-500">BMI (Body Mass Index) is weight divided by square of height. It is a screening measure, not a diagnostic tool.</p>
              </div>
            </div>
          ) : (
            <p className="mt-2 text-sm text-gray-500">Enter your weight and height, then click Calculate to see your BMI.</p>
          )}
        </div>

        <div className="mt-4 p-3 text-xs rounded-md border bg-slate-50 dark:bg-slate-800 text-gray-600">
          <strong>Note:</strong> BMI is a simple screening tool and doesn't account for muscle mass, bone density, or distribution of fat. For medical advice, consult a professional.
        </div>
      </div>
    </div>
  );
}

```
## OUTPUT
<img width="1415" height="1024" alt="Screenshot 2025-11-26 214925" src="https://github.com/user-attachments/assets/5a9e906a-9184-4285-968a-90658c653666" />


## RESULT
The program for creating BMI Calculator using React Router is executed successfully.
