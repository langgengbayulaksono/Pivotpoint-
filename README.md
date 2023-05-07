# Pivotpoint-
Pivot point 

//+------------------------------------------------------------------+
//|                                                  PivotPoint.mq4  |
//|                          Copyright © 2023, Your Name             |
//|                                       https://www.example.com    |
//+------------------------------------------------------------------+

#property copyright "Copyright © 2023, Your Name"
#property link      "https://www.example.com"
#property version   "1.00"
#property strict
#property indicator_chart_window
#property indicator_buffers 11
#property indicator_plots   11

#property indicator_label1  "Pivot Point"
#property indicator_type1   DRAW_LINE
#property indicator_color1  clrRed
#property indicator_width1  1

#property indicator_label2  "Support 1"
#property indicator_type2   DRAW_LINE
#property indicator_color2  clrBlue
#property indicator_width2  1

#property indicator_label3  "Support 2"
#property indicator_type3   DRAW_LINE
#property indicator_color3  clrBlue
#property indicator_width3  1

#property indicator_label4  "Support 3"
#property indicator_type4   DRAW_LINE
#property indicator_color4  clrBlue
#property indicator_width4  1

#property indicator_label5  "Support 4"
#property indicator_type5   DRAW_LINE
#property indicator_color5  clrBlue
#property indicator_width5  1

#property indicator_label6  "Support 5"
#property indicator_type6   DRAW_LINE
#property indicator_color6  clrBlue
#property indicator_width6  1

#property indicator_label7  "Resistance 1"
#property indicator_type7   DRAW_LINE
#property indicator_color7  clrGreen
#property indicator_width7  1

#property indicator_label8  "Resistance 2"
#property indicator_type8   DRAW_LINE
#property indicator_color8  clrGreen
#property indicator_width8  1

#property indicator_label9  "Resistance 3"
#property indicator_type9   DRAW_LINE
#property indicator_color9  clrGreen
#property indicator_width9  1

#property indicator_label10 "Resistance 4"
#property indicator_type10  DRAW_LINE
#property indicator_color10 clrGreen
#property indicator_width10 1

#property indicator_label11 "Resistance 5"
#property indicator_type11  DRAW_LINE
#property indicator_color11 clrGreen
#property indicator_width11 1

input ENUM_TIMEFRAMES TimeFrame = PERIOD_D1; // Timeframe to calculate the pivot points

// Indicator buffers
double Pivot[];
double Support1[];
double Support2[];
double Support3[];
double Support4[];
double Support5[];
double Resistance1[];
double Resistance2[];
double Resistance3[];
double Resistance4[];
double Resistance5[];

//+------------------------------------------------------------------+
//| Custom indicator initialization function                         |
//+------------------------------------------------------------------+
int OnInit()
{
   SetIndexBuffer(0, Pivot);
   SetIndexBuffer(1, Support1);
   SetIndexBuffer(2, Support2);
   SetIndexBuffer(3, Support3);
   SetIndexBuffer(4, Support4);
   SetIndexBuffer(5, Support5);
   SetIndexBuffer(6, Resistance1);
   SetIndexBuffer(7, Resistance2);
   SetIndexBuffer(8, Resistance3);
   SetIndexBuffer(9, Resistance4);
   SetIndexBuffer(10, Resistance5);

   IndicatorDigits(Digits);

   return(INIT_SUCCEEDED);
}

//| Custom indicator iteration function                              |
//+------------------------------------------------------------------+
int OnCalculate(const int rates_total, const int prev_calculated, const datetime &time[], const double &open[], const double &high[], const double &low[], const double &close[], const long &tick_volume[], const long &volume[], const int &spread[])
{
   for (int i = 0; i < rates_total; i++)
   {
      Pivot[i] = EMPTY_VALUE;
      Support1[i] = EMPTY_VALUE;
      Support2[i] = EMPTY_VALUE;
      Support3[i] = EMPTY_VALUE;
      Support4[i] = EMPTY_VALUE;
      Support5[i] = EMPTY_VALUE;
      Resistance1[i] = EMPTY_VALUE;
      Resistance2[i] = EMPTY_VALUE;
      Resistance3[i] = EMPTY_VALUE;
      Resistance4[i] = EMPTY_VALUE;
      Resistance5[i] = EMPTY_VALUE;
   }

   datetime current_time = time[0];
   int current_period = iBarShift(Symbol(), TimeFrame, current_time);
   datetime start_period = iTime(Symbol(), TimeFrame, current_period);

   if (TimeFrame < Period())
   {
      Print("Error: The specified TimeFrame is lower than the current chart period.");
      return(0);
   }

   if (start_period != iTime(Symbol(), TimeFrame, current_period + 1))
   {
      // Find the highest and lowest prices for the specified timeframe
      double highest = iHigh(Symbol(), TimeFrame, current_period + 1);
      double lowest = iLow(Symbol(), TimeFrame, current_period + 1);
      double close_price = iClose(Symbol(), TimeFrame, current_period + 1);

      // Calculate Pivot Point, Supports and Resistances
      double pp = (highest + lowest + close_price) / 3;
      double s1 = (pp * 2) - highest;
      double s2 = pp - (highest - lowest);
      double s3 = s1 - (highest - lowest);
      double s4 = s2 - (highest - lowest);
      double s5 = s3 - (highest - lowest);
      double r1 = (pp * 2) - lowest;
      double r2 = pp + (highest - lowest);
      double r3 = r1 + (highest - lowest);
      double r4 = r2 + (highest - lowest);
      double r5 = r3 + (highest - lowest);

      // Assign the calculated values to the corresponding buffers
      for (int i = 0; i < rates_total; i++)
      {
         Pivot[i] = pp;
         Support1[i] = s1;
         Support2[i] = s2;
         Support3[i] = s3;
         Support4[i] = s4;
         Support5[i] = s5;
         Resistance1[i] = r1;
         Resistance2[i] = r2;
         Resistance3[i] = r3;
         Resistance4[i] = r4;
         Resistance5[i] = r5;
      }
    // Send notification if the current high price is greater than the Resistance levels
   if (high[0] > Resistance1[current_period] && high[1] <= Resistance1[current_period])
   {
      SendNotification("Resistance 1 level is broken!");
   }
   if (high[0] > Resistance2[current_period] && high[1] <= Resistance2[current_period])
   {
      SendNotification("Resistance 2 level is broken!");
   }
   if (high[0] > Resistance3[current_period] && high[1] <= Resistance3[current_period])
   {
      SendNotification("Resistance 3 level is broken!");
   }
   if (high[0] > Resistance4[current_period] && high[1] <= Resistance4[current_period])
   {
      SendNotification("Resistance 4 level is broken!");
   }
   if (high[0] > Resistance5[current_period] && high[1] <= Resistance5[current_period])
   {
      SendNotification("Resistance 5 level is broken!");
   }

   // Send notification if the current low price is lower than the Support levels
   if (low[0] < Support1[current_period] && low[1] >= Support1[current_period])
   {
      SendNotification("Support 1 level is broken!");
   }
   if (low[0] < Support2[current_period] && low[1] >= Support2[current_period])
   {
      SendNotification("Support 2 level is broken!");
   }
   if (low[0] < Support3[current_period] && low[1] >= Support3[current_period])
   {
      SendNotification("Support 3 level is broken!");
   }
   if (low[0] < Support4[current_period] && low[1] >= Support4[current_period])
   {
      SendNotification("Support 4 level is broken!");
   }
   if (low[0] < Support5[current_period] && low[1] >= Support5[current_period])
   {
      SendNotification("Support 5 level is broken!");
   }

   }

   return(rates_total);
}
//+------------------------------------------------------------------+
