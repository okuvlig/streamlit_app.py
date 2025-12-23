# streamlit_app.py
requirements.txt
marketpulse
async function analyzeStock() {
    const tickerInput = document.getElementById('tickerInput');
    const ticker = tickerInput.value.trim().toUpperCase();
    
    if (!ticker) {
        document.getElementById('signalOutput').innerText = "⚠️ Please enter a ticker symbol (e.g., AAPL).";
        return;
    }

    const apiKey = 'YOUR_FREE_ALPHA_VANTAGE_KEY'; // ⚠️ Replace in production with backend call
    const url = `https://www.alphavantage.co/query?function=GLOBAL_QUOTE&symbol=${ticker}&apikey=${apiKey}`;

    try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(`HTTP error! Status: ${response.status}`);
        
        const data = await response.json();

        // Handle Alpha Vantage errors
        if (data.Note) throw new Error("API rate limit exceeded. Try again in 1 minute.");
        if (data['Error Message']) throw new Error(data['Error Message']);
        if (!data['Global Quote']?.['05. price']) throw new Error("Invalid ticker or no data available.");

        const price = parseFloat(data['Global Quote']['05. price']);
        
        // Simulated indicators (for demo only)
        const sma200 = price * (0.92 + Math.random() * 0.1); 
        const rsi = 30 + Math.random() * 40; 

        let signal = "";
        if (price > sma200 && rsi < 40) {
            signal = "STRONG BUY: Bullish Trend + Oversold";
        } else if (price < sma200) {
            signal = "SELL: Bearish Trend Detected";
        } else {
            signal = "HOLD: Market is Neutral";
        }

        document.getElementById('priceDisplay').innerText = `$${price.toFixed(2)}`;
        document.getElementById('signalOutput').innerText = signal;
        document.getElementById('signalOutput').className = "signal"; // Optional: add CSS class

    } catch (error) {
        console.error("Analysis error:", error);
        document.getElementById('signalOutput').innerText = `❌ Error: ${error.message}`;
    }
}
