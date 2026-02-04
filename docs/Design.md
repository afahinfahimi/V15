# Lovable Trades
A Market and Stock Analysis and Backtesting Application

The app uses mathematical tests based on different market data, technical analysis and price movements to score the market and individual stocks.
In combination with alerts and other ai assessment help, it helps the user find good trading opportunities by direct signals or indirect delivery of useful information.
Another important role of the app is backtesting the scoring systems. It runs the analysis on existing data from the past, and uses that to improve the accuracy of the tests.
The app provides these results through a few pages. 

## Pages

* Stock Analysis
The main page of the app that is the starting point. User enters or uploads the list of symbols and the app runs the tests to score and deliver the results in this page.

* Backtesting
This page runs the same analysis and scoring on days in the past and shows the results and compares it with how stocks did and how corelated scores and price movements are.

Matrices
A set of tables that populates results of multiple stocks in a single day, or a single stock or market conditioin in several days. 
The data is delivered via an organized table, as well as visual charts and can be exported into a CSV file.

* Watchlists


### Stock Analysis

### Row One
- Logo on the left
- Navigation menu on the right

### Result Table
Rows:
1. Header
2. MC (Market Condition) - All data is from QQQ as MC proxy. Details Below
3. Stocks details rows (details below)
4. Beta row with the purpose of comparing the performance of the list with the market (QQQ) (details below)

Col 1
Checkbox (circle shape)
Selecting the checkbox displays buttons on top of the table for:
 - Add to watchlist
 - Remove from watchlist
 - Stock Matrix
 - Score matrix if multiple checkboxes are selected

MC: Check box that opens button for 'MC Matrix'
BETA: ---

Col 2
Symbol 
Design: Size: 18px in white allcap text
Second Line: Company sector and Name in small gray font. Truncated after two words
Toggle to Company Summary. 

MC: QQQ
BETA: BETA

Col 3
SA Score 
Design: In a circle with SA color coded background based on the score.
Toggle to: Breakdown of the answers and points given

MC: MC Score
BETA: 

Col 4
Alert Icon
Design: Alert Icon in red/green/yellow based on the type of alert. If multiple alerts, pick the higher urgency color
Toggle: List of alerts and description of them

MC: Market Alerts
BETA: ---

Col 5:
Tier + Max Portfolio % Limit separated with a '.'
Design: Reactangle with background of tiers colors. All buttons save width regardless of content width
Tooltip: Show the Tier defenitin about the company

MC: T1 . $200k
BETA: ---

Col 6:
MS Score
Design: Transparent equare with rounded borders, with 0.5 px and number in MS color coding
Toggle: How the MS Contrarian Score is calulated. Also show VIX

MC: ---
BETA: ---

Col 7:
MS Signal
Design: Rectangle transparent with border and text in signal color code. All recangles to have the same width regardless of text width

MC: MC Level Signal
BETA: ---

Col 8:
Price (todays)
2nd Line: After-Hour % change in small text if any
Toggle: Details of price change % for 1d, 5d, 1m, 3m, 1yr a vertical separator then after-hour - change 1 day ago, change 2 days ago, change 3 days ago, change 4 days ago

MC: QQQ Price
Toggle: Details of price change % for 1d, 5d, 1m, 3m, 1yr a vertical separator then after-hour - change 1 day ago, change 2 days ago, change 3 days ago, change 4 days ago
BETA: ---

Col 9: 
1-Day price change % (live during market hours or after hours if available)

MC: QQQ 1-Day price change %
BETA: Average % of stocks in the table

Col 10:
5-Day price change %

MC: QQQ 5-Day price change %
BETA: Average % of stocks in the table

Col 11:
1M price change %

MC: QQQ 1M price change %
BETA: Average % of stocks in the table

Col 12:
3M price change %

MC: MC Score
BETA: Average % of stocks in the table


























