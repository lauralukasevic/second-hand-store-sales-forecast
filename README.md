# Second Hand Store: sales forecast

Sales forecast for a second-hand store using SQL (PostgreSQL), Python and machine learning, with a dashboard answering 4 business questions.

**Tools:** SQL (PostgreSQL), Python, pandas, scikit-learn, SQLAlchemy, matplotlib, Jupyter

The notebook creates 10 years of sales (2017 to 14 Nov 2026), stores them in **PostgreSQL**, forecasts the next 12 months for every product with **machine learning**, and answers 4 business questions with charts. The data is **synthetic** (generated in the notebook with a fixed random seed, so it is the same every run). The results show how the method works, not real market facts.

## Dashboard
The results are also available as a finished dashboard:

- **PDF:** [dashboard.pdf](dashboard.pdf), the printed version. GitHub shows it directly in the browser.
- **HTML:** [dashboard.html](dashboard.html), the interactive page. Click a product to see its forecast, or a row in the table. GitHub shows HTML files as code, so download the file and open it in a browser. You can also view it online with GitHub Pages: [open the page](https://your-username.github.io/second-hand-store-sales-forecast/dashboard.html)

The dashboard shows the KPI cards, a forecast chart for every product, a table of all products, revenue by category and the answers to the 4 business questions.

## Business questions
1. Which products will bring in the most revenue next year?
2. Which products are growing and which are shrinking?
3. When should the store stock up (which months are busy)?
4. Which forecasts can be trusted?

## Files
| File | What it is |
|---|---|
| `secondhand_project_v4.ipynb` | The main project: data, database, model, charts and conclusions |
| `secondhand_dashboard.ipynb` | Dashboard with charts, a results table and the 4 business questions (charts show directly on GitHub) |
| `secondhand_website.ipynb` | Builds the dashboard as a web page and saves it as `dashboard.html` |
| `dashboard.html` | The dashboard as an interactive web page |
| `dashboard.pdf` | The dashboard, printed as a PDF |
| `requirements.txt` | Python libraries needed |
| `README.md` | This file |

## How to run it
1. **Start PostgreSQL** (I use Postgres.app on a Mac) and create a database called `project3_sql`.
2. **Install the libraries:**
   ```
   pip install -r requirements.txt
   ```
3. **Open the main notebook** and run it from top to bottom (Kernel > Restart & Run All):
   ```
   jupyter notebook secondhand_project_v4.ipynb
   ```
   It creates the data and loads it into PostgreSQL.
4. **Optional:** run `secondhand_dashboard.ipynb` for the dashboard charts, or `secondhand_website.ipynb` to rebuild `dashboard.html`. Both read the data from PostgreSQL, so run the main notebook first.
5. If the connection fails, check `DB_URL` in the settings cell of each notebook. It has my Mac username (`lauralukasevic`), so replace it with yours. Postgres.app needs no password.

The main notebook drops and recreates its tables every time it runs, so running it again deletes the old data in those tables.

## What the main notebook does
| Section | Content |
|---|---|
| 1 to 2 | Imports, connection to PostgreSQL |
| 3 | Generates customers, products, orders and order items (with yearly growth, seasonality and a COVID dip in 2020) |
| 4 | Creates the tables and loads the data |
| 5 | Overview charts: revenue by category, year and country, items sold by product |
| 6 to 7 | Machine learning functions, backtest and the 12-month forecast |
| 8 | Baseline comparison |
| 9 | The 4 business questions, one chart each |
| 10 | Forecast chart for one product |
| 11 | Conclusions |

## Database
Four tables with primary keys, foreign keys, `CHECK` constraints and indexes:

`customers` <- `orders` <- `order_items` -> `products`

## How the forecast works
- **Model:** gradient boosting (`GradientBoostingRegressor`), one model per product, because every product has its own season and level.
- **Inputs:** sales 1, 2, 3, 6 and 12 months ago, the average of the last 3 months, the month of the year and a time counter.
- **Backtest:** the last 12 months are hidden, forecast, and compared with what really sold. This gives the forecast error (MAPE).
- **Baseline:** a simple rule, "sales this month = the same month last year". The model is only worth using if it beats this rule.

## Results
- Forecast revenue for Nov 2026 to Oct 2027: about **€242k** (about +4% vs 2025).
- Top revenue: Retro Sneakers, Vintage Denim Jacket and Used Smartphone (about €34k each).
- Fastest growing: Paperback Books Box, Oak Bookshelf and Ceramic Dinner Set. The Mid-century Armchair is the only clear decline.
- Store-wide sales peak in October and November and are lowest in March. Each product has its own season.
- The model beats the baseline on **10 of 12 products** (average error 26.3% vs 30.2%). It does not on the Mid-century Armchair and the Vintage Camera, which sell too few items per month to predict well.

## Limitations
- The data is synthetic, so real stores will be messier.
- The model tends to forecast too low for growing products, because tree models cannot predict above values they have seen.
- Low-volume products (furniture, cameras) have high forecast errors.
- 2026 contains data only until 14 November, so it looks lower than full years. The unfinished month is left out of the model.

## Next steps
- Add a trend feature or compare with Holt-Winters to reduce the low bias.
- Try real marketplace data instead of synthetic data.

## Tested with
Python with pandas 3.1, numpy 2.5, scikit-learn 1.9, matplotlib 3.11, SQLAlchemy 2.1, psycopg2-binary 2.9 and PostgreSQL 18.
