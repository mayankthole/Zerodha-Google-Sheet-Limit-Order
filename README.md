Here you are taking the input values for the trade from the google sheet, and you are placing the best Bid and best Ask automatically based on the code logic that you have defined.

# Zerodha Limit Order Trading Bot

## 🚀 What This Does
This is an automated trading bot that reads orders from Google Sheets and places limit orders on Zerodha using real-time market quotes.

## 📁 Project Structure
```
├── place_order1_working.py    # Main trading bot (WORKING VERSION)
├── service_account.json        # Google Cloud service account credentials
├── access_token.txt           # Zerodha access token (if needed)
├── instruments.csv            # NOT NEEDED - Zerodha handles mapping automatically
├── requirements.txt           # Python dependencies
└── Version_history/          # Backup versions of the code
```

## 🔑 Key Features
- **Automatic Exchange Detection**: NSE, NFO, CDS based on symbol
- **Real-time Price Quotes**: Gets best bid/ask prices automatically
- **Google Sheets Integration**: Reads orders from 'Place_Orders' sheet
- **Smart Order Management**: Skips already processed orders
- **Margin Checking**: Automatic via Zerodha API

## 📊 How It Works

### 1. **Order Flow**
```
Google Sheet → Bot Reads → Places Order → Updates Status
```

### 2. **Exchange Detection Logic**
- **NSE**: No numbers in symbol (e.g., RELIANCE, SBIN)
- **NFO**: 2+ numbers in symbol (e.g., BANKNIFTY24JAN50000CE)
- **CDS**: Currency derivatives (e.g., USDINR, EURINR)

### 3. **Price Setting**
- **BUY Orders**: Uses best bid price (what buyers are willing to pay)
- **SELL Orders**: Uses best ask price (what sellers are asking)

## 🛠️ Setup Instructions

### 1. **Install Dependencies**
```bash
pip install -r requirements.txt
```

### 2. **Google Sheets Setup**
- Create a Google Sheet with ID: `1xHoWl9HZdpuRVM9h_WLuPeeCd4CZAhIDpoeYVfvHTE`
- Add 'Info' sheet with:
  - B1: API Key
  - B2: API Secret  
  - B3: Access Token
- Add 'Place_Orders' sheet with columns:
  - A: Symbol (e.g., RELIANCE)
  - B: Direction (BUY/SELL)
  - C: Quantity
  - D: Status (auto-updated)
  - E: Timestamp (auto-updated)
  - F: Limit Price (auto-updated)

### 3. **Zerodha Setup**
- Get API credentials from Zerodha
- Put them in the Google Sheet 'Info' tab
- The bot will automatically handle access tokens

## 🚀 How to Run

### **Local Development**
```bash
python place_order1_working.py
```

### **GCP Cloud Functions**
- Deploy `place_order1_working.py` as a Cloud Function
- Set trigger to run every 10 seconds or as needed

## ⚙️ Configuration

### **Timing Settings**
- **Row Processing**: 1 second delay between rows
- **Cycle Pause**: 10 seconds between complete cycles
- **Polling**: Runs continuously

### **Order Processing**
- **Starts from**: Row 2 (Row 1 is header)
- **Processes**: All rows without "ORDER_PLACED" status
- **Updates**: Status, timestamp, and limit price automatically

## 🔍 Understanding the Code

### **Main Functions**
- `place_order()`: Places individual orders with auto-detection
- `process_place_orders()`: Main loop that reads from Google Sheets
- `get_credentials_from_sheet()`: Gets API keys from Google Sheets

### **What You DON'T Need**
- ❌ `instruments.csv` - Zerodha handles stock mapping automatically
- ❌ `get_instrument_token()` - Unused function
- ❌ `get_quote()` - Unused utility function

### **What You DO Need**
- ✅ Google Sheets with proper structure
- ✅ Zerodha API credentials
- ✅ Service account JSON file

## 📈 Example Usage

### **Google Sheet Entry**
```
| Symbol   | Direction | Quantity | Status | Timestamp | Limit Price |
|----------|-----------|----------|---------|-----------|-------------|
| RELIANCE | BUY       | 100      |         |           |             |
| SBIN     | SELL      | 50       |         |           |             |
```

### **Bot Output**
```
[11:42:22] Polling Place_Orders...
Auto-detected exchange: NSE for symbol RELIANCE
Auto-setting product to CNC for NSE exchange
Auto-setting BUY limit price to best bid: ₹1415.8
Order placed: 1958774250936737792
```

## 🚨 Important Notes

### **Margin Checking**
- Bot automatically checks margin before placing orders
- Failed orders due to insufficient funds are logged but not updated in sheet
- These orders will be retried in next cycle

### **Error Handling**
- Invalid symbols: Logged and skipped
- API errors: Logged and row status unchanged
- Network issues: Automatic retry in next cycle

### **Order Status**
- **Empty/Blank**: Will be processed
- **ORDER_PLACED**: Will be skipped
- **Any other status**: Will be processed

## 🔧 Troubleshooting

### **Common Issues**
1. **"instruments.csv file not found"** - Ignore this, file not needed
2. **"Access token expired"** - Bot will prompt for new login
3. **"Insufficient funds"** - Check your Zerodha account balance

### **Debug Mode**
- All actions are logged with timestamps
- Check console output for detailed information
- Google Sheet status updates show processing results

## 📚 Version History
- **place_order1_working.py**: Current working version
- **Version_history/**: Contains backup versions and development history

## 🤝 Support
- Check console logs for error messages
- Verify Google Sheet structure matches requirements
- Ensure Zerodha API credentials are correct
- Check internet connectivity for API calls

---
**Last Updated**: Current working version
**Status**: ✅ Production Ready
**Deployment**: GCP Cloud Functions compatible
