# Crypto-Trading-bot
pip install python-binance
#basic bot
import logging
from binance.client import Client
from binance.enums import *

# Setup logging
logging.basicConfig(
    filename='trading_bot.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)


class BasicBot:
    def __init__(self, api_key, api_secret, testnet=True):
        try:
            self.client = Client(api_key, api_secret)
            if testnet:
                self.client.FUTURES_URL = 'https://testnet.binancefuture.com/fapi'
            logging.info("Binance client initialized successfully.")
        except Exception as e:
            logging.error(f"Error initializing client: {e}")

    def get_balance(self, asset="USDT"):
        try:
            balance_info = self.client.futures_account_balance()
            for balance in balance_info:
                if balance['asset'] == asset:
                    logging.info(f"Balance retrieved: {balance}")
                    return float(balance['balance'])
        except Exception as e:
            logging.error(f"Failed to get balance: {e}")
            return None

    def place_market_order(self, symbol, side, quantity):
        try:
            order = self.client.futures_create_order(
                symbol=symbol,
                side=side,
                type=ORDER_TYPE_MARKET,
                quantity=quantity
            )
            logging.info(f"Market order placed: {order}")
            return order
        except Exception as e:
            logging.error(f"Order placement failed: {e}")
            return None

    def get_price(self, symbol="BTCUSDT"):
        try:
            ticker = self.client.futures_symbol_ticker(symbol=symbol)
            price = float(ticker['price'])
            logging.info(f"Current price of {symbol}: {price}")
            return price
        except Exception as e:
            logging.error(f"Failed to fetch price: {e}")
            return None


# Example usage
if __name__ == "__main__":
    API_KEY = "your_testnet_api_key"
    API_SECRET = "your_testnet_api_secret"

    bot = BasicBot(API_KEY, API_SECRET)

    balance = bot.get_balance()
    print(f"USDT Balance: {balance}")

    price = bot.get_price("BTCUSDT")
    print(f"BTCUSDT Price: {price}")

    # Example: Buy 0.001 BTC
    order = bot.place_market_order("BTCUSDT", SIDE_BUY, 0.001)
    print("Order Response:", order)
