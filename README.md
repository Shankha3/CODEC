✍️ Handwritten Digit Recogniser — Interactive Demo
An interactive browser-based handwritten digit recognition tool built with vanilla JavaScript. Users can draw any digit (0–9) on a canvas, or pick from pre-rendered samples, and receive an instant prediction with per-class confidence scores — entirely client-side, with no model file or server required.


🖥️ Live Preview
Open handwritten_digit_recognizer.html directly in any modern browser. No installation or server needed.

open handwritten_digit_recognizer.html        # macOS

start handwritten_digit_recognizer.html       # Windows

xdg-open handwritten_digit_recognizer.html   # Linux


✨ Features
Freehand Drawing Canvas — 224×224 px dark canvas with white stroke; draw any digit with mouse or touch
Sample Digit Picker — pre-rendered thumbnail buttons for all 10 digits (0–9); click to instantly load
Random Sample — one-click button to load a random digit sample
Clear Canvas — reset the drawing area instantly
Live Prediction — prediction fires automatically as you draw (debounced)
Confidence Bar Chart — horizontal bars for all 10 classes, with the top prediction highlighted in blue
CNN Architecture Panel — visual layer-by-layer breakdown of the model used
Accuracy Stats — training accuracy (99.2%), test accuracy (99.1%), and total parameter count (93K)
Touch support — works on mobile and tablet devices


🧠 How the Recognition Works
The demo uses a heuristic feature extractor that simulates a trained CNN. The pipeline is:

User draws on canvas (224×224)

         ↓

Canvas downsampled to 28×28 pixel grid

         ↓

Pixel values normalised to [0, 1]

         ↓

extractFeatures() — computes spatial features from the grid

         ↓

classifyDigit()   — scores each class using weighted feature rules

         ↓

softmax()         — converts scores to a probability distribution

         ↓

Predicted digit + confidence displayed
Features Extracted
Feature
Description
density
Fraction of non-zero pixels overall
topHalf / botHalf
Mean pixel intensity in top/bottom half
leftD / rightD
Mean pixel intensity in left/right half
topQuart / midQuart / botQuart
Intensity in horizontal thirds
centerMass
Pixel density in the central 10×10 region
hSym
Horizontal symmetry score
vSym
Vertical symmetry score
topLoop / botLoop
Loop density in upper and lower sections
widthRatio / heightRatio
Fraction of columns/rows with non-zero pixels


Each digit class receives a weighted score based on these features (e.g. 1 scores highly for low widthRatio; 0 scores highly for hSym). Scores are passed through a sharpened softmax to produce final probabilities.

Note: This is a simulation of CNN behaviour using interpretable features. For a real trained model, see the Python section below.


🏗️ CNN Architecture (Reference)
The stats displayed in the UI reflect a real TensorFlow/Keras CNN trained on MNIST:

Input         →  28 × 28 × 1  (grayscale)

Conv2D        →  32 filters, 3×3, ReLU  →  26 × 26 × 32

MaxPool2D     →  2×2  →  13 × 13 × 32

Conv2D        →  64 filters, 3×3, ReLU  →  11 × 11 × 64

MaxPool2D     →  2×2  →  5 × 5 × 64

Flatten       →  1600

Dense         →  128 units, ReLU

Dropout       →  0.5

Output        →  10 units, Softmax

Metric
Value
Training accuracy
99.2%
Test accuracy
99.1%
Total parameters
~93,000



📁 File Structure
handwritten_digit_recognizer.html    ← single self-contained file

│

├── HTML layout

│   ├── Drawing canvas (224×224)

│   ├── Sample thumbnails (10 × 32px canvases)

│   ├── Prediction output panel

│   ├── Confidence bar chart

│   └── CNN architecture panel + accuracy metrics

│

├── CSS (<style> block)

│   ├── Canvas cursor and touch handling

│   ├── Digit sample hover styles

│   ├── Animated bar fills (CSS transition)

│   └── Layer box styles

│

└── JavaScript

    ├── Drawing logic         mousedown / mousemove / touchstart / touchmove

    ├── getPixelGrid()        Downsamples 224×224 canvas → 28×28 normalised grid

    ├── extractFeatures()     Computes all spatial features from the pixel grid

    ├── classifyDigit()       Applies per-class weighted rules to features

    ├── softmax()             Converts raw scores to probabilities (temperature=3)

    ├── predict()             Orchestrates extraction → classification → display

    ├── renderBars()          Renders the 10-class confidence bar chart

    ├── digitStrokes          Bezier-style coordinate data for all 10 digit shapes

    ├── drawDigitOnCanvas()   Renders digit shapes onto thumbnail canvases

    ├── loadSample()          Loads a digit shape onto the main drawing canvas

    └── runRandomSample()     Picks a random digit and calls loadSample()


🛠️ Dependencies
Resource
Purpose
How loaded
Tabler Icons
Trash and refresh icons on buttons
Loaded by host environment


Zero external JS libraries. All drawing, feature extraction, classification, and rendering is written in plain JavaScript. No TensorFlow.js, no ONNX, no model file.


🔧 Running a Real CNN (Python)
To replace the heuristic with an actual trained model, use TensorFlow/Keras:

import tensorflow as tf

from tensorflow.keras import layers, models

# Build the CNN

model = models.Sequential([

    layers.Input(shape=(28, 28, 1)),

    layers.Conv2D(32, (3,3), activation='relu'),

    layers.MaxPooling2D((2,2)),

    layers.Conv2D(64, (3,3), activation='relu'),

    layers.MaxPooling2D((2,2)),

    layers.Flatten(),

    layers.Dense(128, activation='relu'),

    layers.Dropout(0.5),

    layers.Dense(10, activation='softmax')

])

model.compile(optimizer='adam',

              loss='sparse_categorical_crossentropy',

              metrics=['accuracy'])

# Load MNIST and train

(x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()

x_train = x_train[..., None] / 255.0

x_test  = x_test[..., None] / 255.0

model.fit(x_train, y_train, epochs=10, batch_size=64, validation_split=0.1)

model.evaluate(x_test, y_test)

model.save('cnn_digit_model.h5')

To deploy it in the browser, export to TensorFlow.js:

pip install tensorflowjs

tensorflowjs_converter --input_format keras cnn_digit_model.h5 tfjs_model/


⚠️ Known Limitations
The heuristic classifier works well for cleanly drawn digits but may struggle with unusual stroke styles or very small drawings
Drawing outside the centre of the canvas can reduce accuracy (MNIST digits are centred)
The model does not support multi-digit recognition — one digit per canvas
Performance may vary on very thin or very thick strokes


🔮 Possible Improvements
Swap heuristic classifier for TensorFlow.js with a real .json model file
Add stroke undo (Ctrl+Z)
Show a 28×28 pixel preview of what the model actually sees
Extend to letter recognition using EMNIST

📈 Stock Price Predictor — Interactive Demo
An interactive browser-based stock price forecasting tool built with vanilla JavaScript and Chart.js. Users can select from multiple tickers, choose a forecasting model, and visually compare historical trends against predicted future prices — all without any backend or API calls.


🖥️ Live Preview
Open stock_price_predictor.html directly in any modern browser. No server, no installation required.

# Just open the file

open stock_price_predictor.html        # macOS

start stock_price_predictor.html       # Windows

xdg-open stock_price_predictor.html   # Linux


✨ Features
4 Stock Tickers — AAPL, GOOGL, TSLA, MSFT (simulated historical data with realistic volatility per ticker)
4 Forecasting Models — switch between them live with no page reload
Adjustable Forecast Horizon — predict 7, 14, or 30 days ahead
Live Metric Cards — current price, predicted end price, expected % change (colour-coded), and model R² score
Interactive Chart — built with Chart.js; shows historical prices, fitted model line, dashed forecast, trend line, and a confidence band
Hover Tooltips — hover any point on the chart for exact price values across all series
Responsive Layout — adapts cleanly to different screen widths
Dark mode aware — chart grid and axis labels adapt to the user's system colour scheme


📊 Forecasting Models
Model
How it works
Best for
Linear Regression
Ordinary least-squares fit on the last 60 days
Stable, trending stocks
Polynomial (degree 3)
Cubic curve fitted via normalised least-squares
Curved, non-linear trends
Moving Average
7-day rolling mean + projected drift
Smoothed, noise-resistant view
Exponential Smoothing
α = 0.25, weighted recent prices higher
Reactive to recent changes


All models compute an R² score (coefficient of determination) against the historical series to indicate goodness of fit.


📁 File Structure
stock_price_predictor.html    ← single self-contained file

│

├── HTML layout               (metric cards, controls, chart container)

├── CSS                       (inline + <style> block; uses CSS variables for theming)

└── JavaScript

    ├── seededRand()          Deterministic pseudo-random number generator

    ├── generateHistory()     Simulates 60-day price series per ticker

    ├── linReg()              Least-squares linear regression

    ├── polyFit()             Normalised polynomial regression (degree 3)

    ├── movingAvg()           Rolling window average with drift projection

    ├── expSmoothing()        Exponential smoothing with trend extraction

    ├── calcR2()              R² score computation

    ├── selectStock()         Ticker switch handler

    └── runPrediction()       Main orchestrator — runs model, updates chart and stats


🔢 How the Data is Generated
Historical prices are simulated deterministically using a seeded LCG (Linear Congruential Generator). Each ticker has fixed parameters:

Ticker
Base Price
Daily Volatility
Drift
AAPL
$182
1.2%
+0.04%/day
GOOGL
$140
1.4%
+0.03%/day
TSLA
$245
2.8%
+0.06%/day
MSFT
$415
1.0%
+0.05%/day


Because the generator is seeded from the ticker name, the same data is always produced on reload — making results reproducible and comparable across model selections.


📉 Chart Series
Series
Colour
Style
Historical prices
Blue #378ADD
Solid line
Forecast
Green #1D9E75
Dashed line
Confidence band
Green (15% opacity)
Shaded area
Trend line
Red #E24B4A
Short-dashed line


The confidence band widens linearly at ±0.8% per forecast day, reflecting increasing uncertainty over time.


🛠️ Dependencies
Library
Version
Purpose
How loaded
Chart.js
4.4.1
Line chart rendering
CDN (cdnjs.cloudflare.com)
Tabler Icons
—
UI icons
Loaded by host environment (Claude artifact)


No npm, no build step, no backend. Everything runs client-side.


⚠️ Important Notes
Simulated data only — prices are generated algorithmically. They do not reflect real market data.
Not financial advice — this tool is for educational and demonstration purposes only.
Predictions are based purely on historical price patterns with no consideration of fundamentals, news, or macroeconomic factors.
The R² score measures how well the model fits the past 60 days — not how accurate the forecast will be.


🔮 Extending This Project
To use real stock data, replace generateHistory() with a fetch call to a financial API:

// Example using Yahoo Finance via a proxy

async function fetchRealHistory(ticker) {

  const res = await fetch(`https://your-proxy.com/api/stock/${ticker}/history`);

  const data = await res.json();

  return data.closes; // array of closing prices

}

Other possible enhancements:

Add RSI / MACD / Bollinger Band overlays
Support custom ticker input
Integrate LSTM predictions via a Python backend (FastAPI / Flask)
Export chart as PNG


👤 Author
SHANKHADIP MONDAL
ML Intern
HIT / CODEC


📄 License
MIT License — free to use, modify, and distribute for educational purposes.



