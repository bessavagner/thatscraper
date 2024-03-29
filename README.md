# thatscraper
[![Documentation Status](https://readthedocs.org/projects/thatscraper/badge/?version=latest)](https://thatscraper.readthedocs.io/en/latest/?badge=latest) ![](https://img.shields.io/github/license/bessavagner/thatscraper) ![](https://img.shields.io/pypi/pyversions/thatscraper)

Scrap more, write less.

![demonstration](thatscraper.gif)

`thatscraper` is a selenium adapter.

>Selenium automates browsers. That's it! What you do with that power is entirely up to you.

<div style="text-align: right;">
<p>from <a href="https://www.selenium.dev/">Selenium official webpage</a></p>
</div>

>Selenium is a simple API to write functional/acceptance tests using Selenium WebDriver. Through Selenium Python API you can access all functionalities of Selenium WebDriver in an intuitive way.

<div style="text-align: right;">
<p>from <a href="https://selenium-python.readthedocs.io/index.html">Selenium with python</a></p>
</div>

Since there are so many websites full of javascript code, scrapping pages using static methods, as those found on Beautifulsoup, gets harder or impossible. With Selenium WebDriver you can get around with it.

However it is very common to make sure the desired element is expected with certain conditions. For that you have to add 'waits' contexts, like:

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Firefox()
driver.get("http://somedomain/url_that_delays_loading")
try:
    # the waiting context with the selection of element by id
    element = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "myDynamicElement"))
    )
finally:
    driver.quit()
```

For a single purpose web scraping single script that means some couple of lines like that. However, for a larger testing or scraping project, that can become anoying. With using `thatscraper` above script becomes:

```python
import thatscraper as ts

crawler = ts.Crawler().goto("http://somedomain/url_that_delays_loading")
element = crawler.element_id("myDynamicElement")
crawler.quit()
```

It is very important to quit the webdriver to avoid memory leakage or overcrowding. Always call `quit()` method when job is done . However, `Crawler` comes with a decorator that make sure the webdriver quits in case of any exception.


## Installation

```bash
$ pip install thatscraper
```

## Usage

`thatscraper` can be used to perform basic actions on webpages, such as clicking buttons, dropdown menu, press keyboard keys, send text or filling forms. It is also suitable to extract data.

Instances of `thatscraper.Crawler` are used to navigate pages, perform actions and select elements.


### Run the webdriver:


```python
import time
import thatscraper

crawler = thatscraper.Crawler()
# open page
crawler.goto("https://phptravels.com/demo/")
# wait long enough so you can check the result
time.sleep(5)
# always quit the driver
crawler.quit()
```

Alternatively, you can crawl pages withou opening browser graphics:

```python
crawler = thatscraper.Crawler(headless=True)

```

### Choosing the webdriver

By default `thatscraper` make use of FireFox webdriver ([geckodriver](https://github.com/mozilla/geckodriver/releases)), however other drivers can be selected. But make sure you have the one of your choosing, and its [path is added to your enviroment variables](https://selenium-python.readthedocs.io/installation.html#drivers). For Linux users, download the werdriver and put it in /usr/bin or /usr/local/bin (windows user, [check this out](https://stackoverflow.com/a/56926716/13599189) in order to see how to do that in your system).

Here's a list of suported browser drivers:

| **Browser**  | **Download link** |
| ----------   | ----------------- |
| **Chrome**   | https://sites.google.com/chromium.org/driver/ |
| **Edge**     | https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/ |
| **FireFox**  | https://github.com/mozilla/geckodriver/releases |
| **Safari**   | https://webkit.org/blog/6900/webdriver-support-in-safari-10/ |

To use other driver, pass it's browser name to ```Crawler``` class:

```python
import time
import thatscraper as ts

crawler = ts.Crawler(browser='chrome')

# extract data from
crawler.goto("https://www.techlistic.com/p/demo-selenium-practice.html")
# wait long enough so you can check the result
time.sleep(5)
# always quit the driver
crawler.quit()
```

### Elements their children

Elements can be selected with one of four methods:

- `element(value, by)`: selects an element based on given attribute `by`, with value `value`. A list of attributes is given by `thatscraper.ATTR_SELECTOR.keys()`.
- `elements(value, by)`: selects all elements based on given attribute `by`, with value `value`.
- `child_of(element, value, by)`: selects an element child of `element` (WebElement) based on given attribute `by`, with value `value`.
- `children_of`: selects all elements child of `element` based on given attribute `by`, with value `value`.

Consider the following section of a page:

```html
<div class="form">
    <input type="text" name="first_name" class="first_name input mb1" placeholder="First Name">
    <input type="text" name="last_name" class="last_name input mb1" placeholder="Last Name">
    <input type="text" name="business_name" class="business_name input mb1" placeholder="Business Name">
    ...
</div>
```

In order to make sure you select the input tags from that div with class="from", and not another input that the page may contain, first you can select that div, and then select its children:

```python
form_element = crawler.element("form", "class name")
fields = crawler.children_of(form_element, "input", "tag name")
``` 

### Sending keys

Sending inputs or keys to and element is performed one of two methods:

- `send(key, value, by)`: send `key` to an element based on given attribute `by`, with value `value`.
- `send(key, element)`: send `key` to previously selected `element`.

Consider the same abov section of a page, and the selected elements `field`. Sending a string to element `<input type="text" name="first_name" class="first_name input mb1" placeholder="First Name">` can be:

```python
crawler.send_to_element(fields[0], "Vagner Bessa")
```

Sending keyboard keys works the same way. Check `thatscraper.Key` or `selenium.webdriver.common.keys.Keys` for valid keys.

## Contributing

Interested in contributing? Check out the contributing guidelines. Please note that this project is released with a Code of Conduct. By contributing to this project, you agree to abide by its terms.

## TODOs

- [ ] Fix loggers.
- [ ] Fix `run_script`: the 'wait' holds execution. Change the expected condition?
- [ ] Remove unnecessary dependencies.
- [ ] Test all methods (current coverage: 67%).
- [ ] Implement data extraction modules and classes.  
- [ ] Link or adapt to a database handler.
- [ ] Implement an API boilerplate builder. That API is to serve data extracted from extractor modules and classes.
- [ ] Add `get_attribute` method.
- [ ] Add `wait` method.
- [ ] Add `switch_to_frame` method.

## License

`thatscraper` was created by Vagner Bessa. It is licensed under the terms of the MIT license.

## Credits

`thatscraper` was created with [`cookiecutter`](https://cookiecutter.readthedocs.io/en/latest/) and the `py-pkgs-cookiecutter` [template](https://github.com/py-pkgs/py-pkgs-cookiecutter).
