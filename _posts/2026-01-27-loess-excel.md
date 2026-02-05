---
title: "LOESS Smoothing in Excel"
date: 2026-01-27
categories: 
  - blog
tags:
  - excel
  - loess
---

If you work with noisy time series data, stratigraphic measurements, or any x-y dataset that needs smoothing, you've probably wished Excel had better built-in options for curve fitting. LOESS (Locally Estimated Scatterplot Smoothing) is a powerful technique for this, but it's not natively available in Excel.

I've packaged up a VBA implementation as a simple add-in that you can install and start using immediately. The original code comes from [Jon Peltier's excellent tutorial](https://peltiertech.com/loess-smoothing-in-excel/), which I've converted into an `.xlam` file for easy distribution.

## What is LOESS?

LOESS is a non-parametric regression method that fits a smooth curve through your data by performing weighted local regressions. Unlike polynomial fitting, it doesn't assume a global functional form, making it ideal for data with complex patterns or local variations.

## Installation

The add-in works on both Windows and Mac.

**Windows:**
1. Download `LOESS.xlam` from the [GitHub repository](https://github.com/datuadiatma/loess-excel)
2. Open Excel and go to File → Options → Add-ins
3. Select "Excel Add-ins" from the dropdown at the bottom and click "Go..."
4. Click "Browse" and select the downloaded `LOESS.xlam` file
5. Check the box next to "LOESS" and click OK

**Mac:**
1. Download `LOESS.xlam` from the [GitHub repository](https://github.com/datuadiatma/loess-excel)
2. Open Excel and go to Tools → Excel Add-ins
3. Click "Browse" and select the downloaded `LOESS.xlam` file
4. Check the box next to "LOESS" and click OK

## How to Use

Once installed, you'll have access to a new function: `=LOESS(x_input, y_input, x_output, smoothing_factor)`

### Function Parameters

- **x_input**: Your independent variable range (depth, age, time, etc.)
- **y_input**: Your dependent variable range (isotope values, concentrations, etc.)
- **x_output**: The x values where you want to evaluate the smoothed curve
- **smoothing_factor**: Number of neighboring points to include in each local regression

### Step-by-Step Example

Let's say you have isotope measurements at different stratigraphic heights and want to smooth the curve:

1. Set up your data with x values (stratigraphic height) in column A and y values (isotope ratios) in column B
2. Create a column (column C) for x_output values where you want the smoothed results
3. Select all the cells in column D where you want the LOESS output to appear
4. Enter the formula: `=LOESS(A2:A100, B2:B100, C2:C100, 15)`
5. Press Enter

The key thing to remember is that LOESS returns an array, so you need to select all the output cells before entering the formula.

```bash

| X_input | Y_input | X_output | Y_output (LOESS) |
|---------|---------|----------|------------------|
| 0       | 2.3     | 0        | =LOESS($A$2:$A$101, $B$2:$B$101, C2:C101, 15) |
| 1       | 2.8     | 1        |                  |
| 2       | 2.1     | 2        |                  |
| ...     | ...     | ...      |                  |


```

### Important Notes

- Your x_output values must fall within the range of your x_input data
- The function won't extrapolate beyond your data range
- x_input and x_output must be sorted
- You can use the same values for x_input and x_output if you want smoothed values at your original x positions

## Get the Add-in

The add-in is available on GitHub: [github.com/datuadiatma/loess-excel](https://github.com/datuadiatma/loess-excel)

All credit for the original VBA implementation goes to Jon Peltier. I've simply packaged it for easier distribution and use.
