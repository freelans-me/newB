import requests
from bs4 import BeautifulSoup


class CurrencyConverter:
    def __init__(self, url):
        self.data = requests.get(url)
        self.soup = BeautifulSoup(self.data.text, 'html.parser')
        self.currency_dict = self.get_currency_dict()

    def get_currency_dict(self):
        currency_table = self.soup.find('table', {'class': 'nb-table'}).find('tbody')
        currency_dict = {}
        for row in currency_table.find_all('tr'):
            currency_name = row.find_all('td')[2].text.strip()
            currency_rate = row.find_all('td')[4].text.strip()
            currency_dict[currency_name] = currency_rate
        return currency_dict

    def convert(self, amount, from_currency, to_currency):
        initial_amount = amount
        if from_currency != 'UAH':
            amount = amount / float(self.currency_dict[from_currency])
        amount = round(amount * float(self.currency_dict[to_currency]), 2)
        return amount


url = 'https://bank.gov.ua/control/uk/publish/article?art_id=38441973'
converter = CurrencyConverter(url)
amount = float(input("Введите сумму: "))
from_currency = input("Введите валюту вашей страны: ").upper()
to_currency = 'USD'
result = converter.convert(amount, from_currency, to_currency)
print(f"{amount} {from_currency} равны {result} {to_currency}")
