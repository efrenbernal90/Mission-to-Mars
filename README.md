# Mission-to-Mars
## Overview:
We are creating a website to display data scrapped from multiple sources from the internet! The information scrapped using a python script is stored into a NoSQL database and displayed using a web application.

### Resources:
Data was scrapped from the following pages:
- https://redplanetscience.com/
- https://spaceimages-mars.com/
- https://galaxyfacts-mars.com
- https://marshemispheres.com/

Additional Resources: 
- MongoDB 5.0.5 
- Python 3.7.10
- Flask 1.1.2
- Flask Pymongo 4.0.1
- Beautiful Soup 4.10.0
- Splinter 0.16
- Bootstrap 3.3.7

Additional Documentation:
- [Mongo](https://docs.mongodb.com/manual/tutorial/getting-started/)
- [Splinter](https://readthedocs.org/projects/splinter/downloads/pdf/latest/)
- [Pymongo](https://flask-pymongo.readthedocs.io/en/latest/)
- [Beautiful Soup](https://beautiful-soup-4.readthedocs.io/en/latest/)
- [Bootstrap](https://bootstrapdocs.com/v3.3.6/docs/css/)

## Results
### The app contains a summary and title of the latest news, scrapped from redplanetscience.com. The following script was used:

```Python
    def mars_news(browser):
      # Visit the mars nasa news site
      url = 'https://redplanetscience.com'
      browser.visit(url)
      # Optional delay for loading the page
      browser.is_element_present_by_css('div.list_text', wait_time=1)

      # Add soup object from html
      html = browser.html
      news_soup = soup(html, 'html.parser')

      # Add try/except for error handling
      try:
          slide_elem = news_soup.select_one('div.list_text')
          slide_elem.find('div', class_='content_title')

          # Use the parent element to find the first `a` tag and save it as `news_title`
          news_title = slide_elem.find('div', class_='content_title').get_text()

          # Use the parent element to find the paragraph text
          news_p = slide_elem.find('div', class_='article_teaser_body').get_text()
      except AttributeError:
          return None, None

      return news_title, news_p
```

### The image was added by scrapping spaceimages-mars.com using

```Python
     def featured_image(browser):
        # Visit URL
        url = 'https://spaceimages-mars.com'
        browser.visit(url)

        # Find and click the full image button
        full_image_elem = browser.find_by_tag('button')[1]
        full_image_elem.click()

        # Parse the resulting html with soup
        html = browser.html
        img_soup = soup(html, 'html.parser')

        try:
            # Find the relative image url
            img_url_rel = img_soup.find('img', class_='headerimage fade-in').get('src')

        except AttributeError:
            return None


        # Use the base URL to create an absolute URL
        img_url = f'https://spaceimages-mars.com/{img_url_rel}'

        return img_url
```

### The facts table was scrapped from galaxyfacts-mars.com using

```Python
      def mars_facts():
    # Add try/except for error handling
    try:
        # Use 'read_html' to scrape the facts table into a dataframe
        df = pd.read_html('https://galaxyfacts-mars.com')[0]

    except BaseException:
        return None

    # Assign columns and set index of dataframe
    df.columns=['Description', 'Mars', 'Earth']
    df.set_index('Description', inplace=True)

    # Convert dataframe into HTML format, add bootstrap
    return df.to_html()
```

### Finally, the hemispheres were added by scrapping marshemispheres.com/ using
```Python
    def mars_hemisphere(browser):
    # 1. Visit url
    url = 'https://marshemispheres.com/'
    browser.visit(url)
    # 2. Create a list to hold the images and titles.
    hemisphere_image_urls = []

    # 3. Write code to retrieve the image urls and titles for each hemisphere.
    html = browser.html
    page = soup(html, 'html.parser')
    div = page.find_all(class_= 'description')

    try:
        for x in div:     
            hemimspheres = {}
            
            hem_url = x.find('a')['href']
            browser.visit(f'https://marshemispheres.com/{hem_url}')
            
            html = browser.html
            doc = soup(html, 'html.parser')
            
            img_url = doc.select_one('div.downloads ul li a').get('href')
            full_img_url = f'https://marshemispheres.com/{img_url}'
            name = doc.select_one('h2.title').get_text()
            hemimspheres = {'img_url': full_img_url, 
                'title': name} 
            
            hemisphere_image_urls.append(hemimspheres)
            
            browser.back()
    except AttributeError:
        return None

    return hemisphere_image_urls
```

### The data was uploaded into flask-Pymongo [See app.py](app.py)

### A sample of the website (note that Bootstrap was used to make hemisphere images into thumbnails and split into a row of four images:
![First_half](Resources/Intro_webpage.png)


![Hemispheres](Resources/Mars_hemispheres.png)

### When viewing in different platforms (i.e. iPad), the images and text are responsive to the width of the page, while thumbnails are taking up the width of the page:
![ipad1](Resources/ipad1.png)


![ipad2](Resources/ipad2.png)

