# Book Market Analytics: Books to Scrape

**Data Science 500 assignment**  
**Student:** Riyaad Kayode  
**Date submitted:** 23 October 2026

## Table of contents

1. Introduction  
2. Data acquisition  
3. Data cleaning and preparation  
4. Exploratory analysis and visualisation  
5. Predictive modelling  
6. Practical recommendations  
7. Limitations  
8. Conclusion  
9. References

## 1. Introduction

Book retailers need evidence when deciding which titles to stock, promote and price competitively. This project investigates the Books to Scrape practice catalogue and follows a complete data science workflow. The objectives were to collect at least 200 products using BeautifulSoup, prepare a clean analytical dataset, investigate pricing patterns across genres and ratings, and build a linear regression model that estimates book price.

The work was completed in a Jupyter Notebook so that the acquisition, cleaning, visualisation and modelling steps are transparent and reproducible.

## 2. Data acquisition

The catalogue was scraped from https://books.toscrape.com/ using Python requests and BeautifulSoup. Since the source site displays pounds, the notebook converts prices at the documented academic rate of one source-currency unit = R23.50 before analysis; all final currency values are in South African rand. The first 15 catalogue pages were collected, giving 300 unique books. For each listing, the notebook captured the title, raw price, star rating word, availability text and product URL. The product page was then visited to capture the book's genre, description and quantity available where shown.

The final dataset contains **300 books across 40 genres**, which exceeds the assignment minimum. A small delay was included between requests, and the original raw fields were saved separately so that the cleaning decisions remain auditable.

## 3. Data cleaning and preparation

The rand sign was removed from the price string and the result was converted to a numeric `price_gbp` field. Rating words were mapped to integers from one to five. Stock text was converted to an `in_stock` indicator, while the available quantity was retained as a numeric field when present. Title length, title word count and description word count were derived as potential modelling features. Empty descriptions were represented by an empty string and therefore a zero word count rather than being silently discarded.

The cleaned dataset has no duplicate product URLs. Prices range from **R238.76 to R1401.54**, with an average price of **R805.18**.

## 4. Exploratory analysis and visualisation

The price histogram shows a broad catalogue range rather than a single typical price point. The genre comparison indicates differences in median price, although genre groups should not be treated as identical commercial segments. The rating comparison does not show a strong linear pricing pattern: the Pearson correlation between rating and price is **0.025**. This suggests that a higher customer rating is not, by itself, a dependable explanation for a higher listed price in this sample.

The highest median-price genres in the scraped sample were **Womens Fiction (R57.36)**, **Christian (R54.00)** and **Politics (R51.99)**. These values are descriptive rather than causal: the catalogue may contain different title mixes and does not provide the commercial variables needed to explain why prices differ.

The accompanying notebook contains four visualisations: the overall price distribution, price by rating, price spread by genre and stock availability. An actual-versus-predicted plot is also included for model evaluation.

## 5. Predictive modelling

A linear regression pipeline was trained using rating, quantity available, stock status, title length, title word count, description word count and one-hot encoded genre. Numeric missing values were median-imputed and scaled, while unseen genres were handled safely through one-hot encoding. The data was split into 240 training books and 60 testing books using a fixed random seed of 42.

| Metric | Test result |
|---|---:|
| Mean absolute error | R7980.80 |
| Root mean squared error | R9205.43 |
| R-squared | -0.498 |

The mean absolute error means that predictions were, on average, about R7980.80 away from the observed test prices. The negative R-squared shows that this feature set did not generalise well on the held-out sample and performed worse than a simple baseline based on the mean price. This is a meaningful result: visible catalogue metadata alone is not sufficient for reliable price estimation in this dataset. The model is therefore best treated as an interpretable baseline, not a production pricing system.

## 6. Practical recommendations

1. **Use genre medians for initial benchmarking.** Retailers can compare a new title with the typical price in its genre before making a promotion or margin decision.
2. **Do not price from star rating alone.** The weak rating-price relationship suggests that rating is better treated as a popularity or satisfaction signal than as a direct price driver.
3. **Collect richer commercial variables.** Publisher, author, format, page count, publication date, discount history, sales volume and demand would probably improve the model substantially.
4. **Flag unusual prices for review.** A future model could identify titles whose listed price is far above or below the expected range, after which a buyer can check edition, condition and promotional context.
5. **Improve metadata quality.** Consistent descriptions, categories and availability fields will make catalogue analytics more dependable.

## 7. Limitations

Books to Scrape is a fictional practice website, so the findings should not be generalised to the entire publishing industry. The data is a snapshot, and it does not include sales, demand, publisher, author, edition or discount information. Linear regression also assumes a relatively simple relationship between the features and price. The negative test R-squared confirms that the current model should not be used for automated commercial decisions without additional data and validation.

## 8. Conclusion

The project delivered an end-to-end, reproducible workflow. BeautifulSoup was used to collect 300 listings, the raw values were cleaned into a structured dataset, and the catalogue was explored with readable visualisations. The modelling stage demonstrated both the value and the limits of a linear regression baseline. The key business conclusion is that genre and catalogue metadata can support exploratory benchmarking, but reliable pricing requires richer information about editions, publishers, demand and sales behaviour.

## 9. References

Books to Scrape (n.d.) *Books to Scrape: A fictional bookstore for web scraping practice*. Available at: https://books.toscrape.com/ (Accessed: 23 October 2026).

Hunter, J.D. (2007) 'Matplotlib: A 2D graphics environment', *Computing in Science & Engineering*, 9(3), pp. 90R95.

Pedregosa, F. et al. (2011) 'Scikit-learn: Machine learning in Python', *Journal of Machine Learning Research*, 12, pp. 2825R2830.

Richardson, L. (2007) *Beautiful Soup Documentation*. Available at: https://www.crummy.com/software/BeautifulSoup/bs4/doc/ (Accessed: 23 October 2026).
