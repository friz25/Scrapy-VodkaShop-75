req
python -m venv venv
venv\Scripts\activate
```
pip install scrapy
```
scrapy startproject vodkascraper

Lets look at our prey:
https://www.whiskyshop.com/scotch-whisky?item_availability=In+Stock
```python
cd vodkascraper
scrapy shell
fetch('https://www.whiskyshop.com/scotch-whisky?item_availability=In+Stock')
products = response.css('div.product-item-info') #все товары (html)
len(products) #100
products.css('a.product-item-link').get() #'<a class="product-item-link" href="https://www.whiskyshop.com/macallan-2005-exceptional-cask-5223-10-2017-release">Macallan 2005 Exceptional Cask #5223-10 2017 Release</a>'
products.css('a.product-item-link::text').get() # 'Macallan 2005 Exceptional Cask #5223-10 2017 Release'
products.css('a.product-item-link::text').getall() # all product names (100 шт)
products.css('span.price::text').get() # '£4,860.00'
products.css('span.price::text').get().replace('£','') # '4,860.00' - Цена
products.css('a.product-item-link').attrib['href'] #'https://www.whiskyshop.com/macallan-2005-exceptional-cask-5223-10-2017-release'
```
в `vodkascraper/vodkascraper/spiders` создать файл `vodkaspider.py`
код
получили все (имя,цена,ссылка) на странице

далее нужно переключение между страницами
```python
scrapy shell
fetch('https://www.whiskyshop.com/scotch-whisky?item_availability=In+Stock')
response.css('a.action.next') # [<Selector xpath="descendant-or-self::a[@class and contains(concat(' ', normalize-space(@class), ' '), ' act
response.css('a.action.next').get() # '<a class="action  next" href="https://www.whiskyshop.com/scotch-whisky?item_availability=In+Stock&amp;p=2" title="Next"
response.css('a.action.next').attrib['href'] # 'https://www.whiskyshop.com/scotch-whisky?item_availability=In+Stock&p=2'
```
снова к коду
дописали

```commandline
cd vodkascraper
scrapy crawl whisky -O whisky.json  
```
результат в файле `whisky.json`
(содержит 730 штук товаров (спаршенных с 7 страниц))