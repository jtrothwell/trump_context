# trump_context
This provides some of the main non-proprietary data used in my analysis of who supports Donald Trump. That paper is available here:
"Explaining Nationalist Political Views: The Case of Donald Trump", https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2822059

File 1. Zip_Disability_Rates_2010_2014_5yr_ACS.csv
File 2. Zip_Pop_Shares_by_Race_2010_2014_5yr_ACS.csv

Data are from U.S. Census Bureau, American Fact Finder, American Community Survey 2010-2014.
UDS mapper was used to match Census ZCTAs to USPS zip codes. 
ZCTA values were collapsed to single zip codes using the average value for each ZCTA, weighted by the ZCTA share of zip code population

18-64 disability rates were age adjusted in two ways: 
a) Regressing the disability rate on the level of population by age group in each zip code and subtracting the actual 
value from the predicted value, age2_adj_dis18_64
b) Regressing the disability rate on the percent of population by age group in each zip code and subtracting the actual 
value from the predicted value, age2_adj_dis18_642

File 3. CZ_Mortality_White_Age_Adjust2014.csv

Data are from CDC Wonder and are age-adjusted by the CDC. County data are aggregated to Commuting zones using
crosswalk provided by Raj Chetty's website (http://www.equality-of-opportunity.org/data/) and weighted by county population.

File 4. IRS_Zip_code_tax_returns_2014.csv

Downloaded directly from IRS website, https://www.irs.gov/uac/soi-tax-stats-individual-income-tax-statistics-2014-zip-code-data-soi

My STATA code is as follows:

	drop if zipcode==99999 | zipcode==0

	ren n1 irs_returns
	ren n2 irs_pop
	ren a02650 irs_income
	format irs_income %15.0fc

	gen irs_mean_hh_income=(irs_income*1000)/irs_returns
	gen irs_ui_pct=n02300/irs_returns
	gen irs_pct_with_Kinc=n01000/irs_returns
	gen irs_avg_K_income=(1000*a01000)/irs_returns
	gen irs_K_inc_reliance=a01000/irs_income
	gen irs_pct_with_LLCinc=n26270/irs_returns
	gen irs_LLC_inc_reliance=a26270/irs_income
	gen irs_avg_mort=(a19300*1000)/irs_returns
	gen irs_mort_inc_ratio=a19300/irs_income
	gen irs_SSI_reliance=a02500/irs_income
	gen irs_EITC_pct=n59660/irs_returns
	gen irs_avg_AGI=(1000*a00100)/irs_returns
	gen irs_mean_ind_income=(irs_income*1000)/irs_pop
	gen irs_pct_with_Wage=n00200/irs_returns
	gen irs_avg_Wage=(1000*a00200)/irs_returns
	gen irs_Wage_reliance=a00200/irs_income

	ren n00300 n_interest
	ren n00600 n_dividends
	ren n00650 n_qdividends

	collapse (first) irs_returns irs_pop irs_income statefips state irs_mean_hh_income- irs_mean_ind_income , by(zipcode)
	ren zipcode zip
	format zip %05.0f
	save ...
