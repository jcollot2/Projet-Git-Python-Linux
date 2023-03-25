# Dash-Bash-Python

## Virtual Machine

## Bash
Our goal is to design a scraping script of a website offering financial information. For this, I chose to scrape  : [google finance](https://www.google.com/finance/quote/PX1:INDEXEURO?hl=fr) with the data of CAC 40

I then studied the code of the site to identify syntax or regular expressions (regex). Then, using the [regex101](https://regex101.com/) site, I was able to forge my regex to efficiently extract information from the site.

I was then able to write a first code allowing me to launch a query with CURL and extract my data with my regex :

    res=$(curl -L https://www.google.com/finance/quote/PX1:INDEXEURO?hl=fr)
    today=($(grep -Po '(?<=<div class="YMlKec fxKbKc">)+(.{0,10})(?=<.*div>)' <<<"$res") )
    last=($(grep -Po '(?<=<div class="P6K39c">)(.{0,8})(?=<.*div>)' <<<"$res") )
	other=($(grep -Po '(?<=<div class="P6K39c">)(.{0,20})(?=<.*div>)' <<<"$res") )

I then did some formatting of the data so that it is ready to save in a CSV file which will be generated if it does not already exist. If the file already exists then it will be modified.

    now=$(echo ${today[0]} | sed 's/,/./g' )
	close=$(echo ${last[0]} | sed 's/,/./g')
	daymin=$(echo ${other[1]} | sed 's/,/./g')
	daymax=$(echo ${other[3]} | sed 's/,/./g')
	yearmin=$(echo ${other[4]} | sed 's/,/./g')
	yearmax=$(echo ${other[6]} | sed 's/,/./g')


	if [ ! -f ./data.csv ]
	then
	{
		echo "timestamp,now,close,daymin,daymax,yearmin,yearmax";
		echo "$(date +%s),$now,$close,$daymin,$daymax,$yearmin,$yearmax";
	} > data.csv
	else
	{
		cat data.csv;
		echo "$(date +%s),$now,$close,$daymin,$daymax,$yearmin,$yearmax";
	} > dataBuf.csv
	cp -f dataBuf.csv data.csv
	rm dataBuf.csv

The output of our script is then a CSV file, as shown below:
|timestamp| now | close | daymin | daymax | yearmin | yearmax |
|--|--|--|--|--|--|--|
|1678201388|7 371.95|7 373.21|7 347.31|7 398.03|5 628.42|7 398.03|
|1678201390|7 371.95|7 373.21|7 347.31|7 398.03|5 628.42|7 398.03|
|1678201392|7 371.95|7 373.21|7 347.31|7 398.03|5 628.42|7 398.03|
|...|...|...|...|...|...|...|


## Crontab

Now that the bash script is complete, we need to generate data to design the python Dashboard. For the script to generate data, it would have to run every 5 mins, for that, I use Crontab.

In order to add a task to the Crontab, I use the following command :    `crontab -e`
We will then modify the file to add our job :

## Dash

You can rename the current file by clicking the file name in the navigation bar or by clicking the **Rename** button in the file explorer.

