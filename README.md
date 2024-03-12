# Monthly-Visitors
Finding the the 3 Top countries that visit in Singapore in the different regions and periods
import numpy as np
import pandas as pd
import datetime
import random
import sys # Import the sys module

Asia_list = [' Brunei Darussalam ',' Indonesia ',' Malaysia ',' Philippines ',' Thailand ',' Viet Nam ',' Myanmar ',' Japan ',' Hong Kong ',' China ',' Taiwan ', ' Korea,Republic Of ',' India ',' Pakistan ',' Sri Lanka ',' Saudi Arabia ',' Kuwait ',' UAE ']

Euro_list = [' United Kingdom ',' Germany ',' France ',' Italy ',' Netherlands ',' Greece ',' Belgium & Luxembourg ',' Switzerland ',' Austria ',' Scandinavia ',' CIS & Eastern Europe ']

Other_list = [' USA ',' Canada ',' Australia ',' New Zealand ',' Africa ']

period = int(input('("1: 1978-1987","2: 1988-1997","3: 1998-2007","4: 2008-2017"):'))
region = int(input('("1: Asia","2: Europe","3: Others"):'))

# User input validation
if period not in [1, 2, 3, 4] or region not in [1, 2, 3]:
    print("Invalid input. Please provide valid values.")
    sys.exit() # Use sys.exit() instead of exit()
           
class VisitorsAnalyticsUtils:
    def __init__(self, period, region):
        self.period = period
        self.region = region

    def loadDataFile(self):
        self.df =pd.read_csv("Int_Monthly_Visitor.csv")
        print(self.df.head())
        #print(self.df.describe())
        #print(self.df.info())
        
    def parseData(self):
        if self.period == 1:
           myperiod = range(0,120)
        elif self.period == 2:
           myperiod = range(120,240)
        elif self.period == 3:
           myperiod = range(240,360)
        elif self.period == 4:
           myperiod = range(360,480)

        if self.region == 1:
           myregion = Asia_list
        elif self.region == 2:
           myregion = Euro_list
        elif self.region == 3:
           myregion = Other_list

        self.data = self.df.loc[myperiod,myregion]
        
        # Correct the replacement of commas and 'na' values
        self.data = self.data.applymap(lambda x: x.replace(',', '') if isinstance(x, str) else x)
        try:
            self.data = self.data.apply(pd.to_numeric, errors='coerce')  # Convert to numeric type
        except pd.errors.PandasError as e:
             print(f"Error converting to numeric: {e}")
             print("Rows causing the error:")
             print(self.data[self.data.applymap(lambda x: pd.to_numeric(x, errors='coerce')).isnull().any(axis=1)])
             sys.exit()
             
        print(self.data.head())

    def top3(self):
        total_sum = self.data.sum(axis=0)  # Sum across columns (axis=0)
        df = pd.DataFrame(total_sum, columns=['TotalVisitors'])
        sorted_df = df.sort_values(by='TotalVisitors', ascending=False)
        top3_countries = sorted_df.head(3)
        print(top3_countries)
       
       
myClass = VisitorsAnalyticsUtils(period, region)
myClass.loadDataFile()
myClass.parseData()
myClass.top3()
