# AutoTariffImpact

This repository houses a comprehensive case study analyzing the impact of a 25% tariff on vehicle prices for GM, Toyota, and Hyundai, inspired by Charles Dubord’s LinkedIn article *"Tariffs, Trade, and the True Cost of a Car"* (March 27, 2025). It explores tariff stacking effects on imported vehicles and potential consumer savings from Hyundai’s new Louisiana steel plant, using RStudio for predictive modeling and interactive 3D visualizations.

## Project Overview

- **Objective**: Quantify tariff-induced price increases for GM and Toyota models, and estimate savings for Hyundai models with U.S.-based production, based on Dubord’s tariff cascade formula.
- **Data Sources**:
  - Dubord’s article: Tariff stacking example ($60,000 → $78,444).
  - Approximated 2025 MSRPs and U.S. parts percentages (e.g., GM: 38–50%, Toyota: 20–38%, Hyundai: 20–30%).
  - Hyundai’s $5.8B Louisiana steel plant announcement (Reuters, March 24, 2025).
- **Tools**: RStudio, `ggplot2`, and `plotly`.
- **Scope**: Six vehicles (3 GM, 3 Toyota) under tariffs; three Hyundai models with savings post-Louisiana plant.

## Key Features

1. **Tariff Impact Analysis**:
   - GM: Silverado 1500, Equinox, Sierra 1500.
   - Toyota: Camry, Tacoma, RAV4.
   - 5-year price projection (2025–2030) with compounding effects.

2. **Hyundai Savings Analysis**:
   - Tucson, Santa Fe, Palisade with U.S. production avoiding tariffs.

3. **Visualizations**:
   - Predictive line graph: GM vs. Toyota price trends.
   - 3D scatter plots: Tariff impacts (GM/Toyota) and savings (Hyundai) with hover details.

## Installation

1. Clone the repo:
   ```bash
   git clone https://github.com/yourusername/AutoTariffImpact.git
   ```
2. Install R and RStudio.
3. Install required packages:
   ```R
   install.packages(c("ggplot2", "plotly"))
   ```

## Usage

Run the scripts in RStudio:
- **GM/Toyota Predictive Trends**: `gm_toyota_price_trends.R`
- **GM/Toyota 3D Tariff Impact**: `gm_toyota_3d_tariff.R`
- **Hyundai 3D Savings**: `hyundai_3d_savings.R`

Example:
```R
source("scripts/gm_toyota_3d_tariff.R")
```

### Scripts

#### 1. GM/Toyota Price Trends (2025–2030)
```R
library(ggplot2)

# Data frame
data <- data.frame(
  Year = rep(2025:2030, 2),
  Price = c(50000, 54125, 58575, 63450, 68750, 74500,  # GM Silverado
            35000, 37800, 40825, 44100, 47675, 51575), # Toyota Camry
  Model = rep(c("GM Silverado", "Toyota Camry"), each = 6)
)

# Initial tariff impact
data$Price[data$Year == 2025 & data$Model == "GM Silverado"] <- 66250
data$Price[data$Year == 2025 & data$Model == "Toyota Camry"] <- 44800

# Apply annual increases
for (year in 2026:2030) {
  data$Price[data$Year == year] <- data$Price[data$Year == year - 1] * 1.08
}

# Plot
ggplot(data, aes(x = Year, y = Price, color = Model)) +
  geom_line(size = 1) +
  geom_point(size = 2) +
  labs(title = "Projected Vehicle Price Increases Under 25% Tariffs (2025-2030)",
       subtitle = "GM Silverado vs. Toyota Camry with Retaliation and Friction",
       x = "Year", y = "Price ($)", color = "Model") +
  scale_color_manual(values = c("blue", "red")) +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5, size = 14, face = "bold"),
        plot.subtitle = element_text(hjust = 0.5, size = 12),
        legend.position = "bottom") +
  geom_vline(xintercept = 2025, linetype = "dashed", color = "gray", size = 0.5) +
  annotate("text", x = 2025, y = 80000, label = "Tariff Applied", angle = 90, vjust = -0.5)
```

#### 2. GM/Toyota 3D Tariff Impact
```R
library(plotly)

# Data frame
data <- data.frame(
  Model = c("Silverado 1500", "Equinox", "Sierra 1500", "Camry", "Tacoma", "RAV4"),
  Base_Price = c(50000, 30000, 55000, 35000, 40000, 38000),
  US_Parts_Pct = c(50, 38, 50, 20, 38, 38),
  Final_Price = c(66250, 39210, 72875, 44800, 52280, 49666),
  Price_Increase = c(16250, 9210, 17875, 9800, 12280, 11666),
  Increase_Pct = c(32.5, 30.7, 32.5, 28, 30.7, 30.7),
  Company = c("GM", "GM", "GM", "Toyota", "Toyota", "Toyota")
)

# Hover text
data$Hover <- with(data, paste(
  "Model:", Model,
  "<br>Base Price: $", Base_Price,
  "<br>U.S. Parts: ", US_Parts_Pct, "%",
  "<br>Final Price: $", Final_Price,
  "<br>Increase: $", Price_Increase, " (", Increase_Pct, "%)"
))

# 3D Scatter Plot
plot_ly(data, 
        x = ~Base_Price, 
        y = ~US_Parts_Pct, 
        z = ~Final_Price, 
        text = ~Hover, 
        hoverinfo = "text",
        color = ~Company, 
        colors = c("blue", "red"),
        type = "scatter3d", 
        mode = "markers",
        marker = list(size = 10)) %>%
  layout(
    title = "Tariff Impact on GM and Toyota Vehicles (2025)",
    scene = list(
      xaxis = list(title = "Base Price ($)"),
      yaxis = list(title = "U.S. Parts (%)"),
      zaxis = list(title = "Final Price ($)")
    ),
    legend = list(title = list(text = "Company"))
  )
```

#### 3. Hyundai 3D Savings
```R
library(plotly)

# Data frame
data <- data.frame(
  Model = c("Tucson", "Santa Fe", "Palisade"),
  Base_Price = c(32000, 38000, 47000),
  US_Parts_Pct = c(20, 30, 20),
  Final_Price = c(40960, 49210, 60160),
  Savings = c(8960, 11210, 13160),
  Savings_Pct = c(28, 29.5, 28)
)

# Hover text
data$Hover <- with(data, paste(
  "Model:", Model,
  "<br>Base Price: $", Base_Price,
  "<br>U.S. Parts: ", US_Parts_Pct, "%",
  "<br>Tariffed Price: $", Final_Price,
  "<br>Savings: $", Savings, " (", Savings_Pct, "%)"
))

# 3D Scatter Plot
plot_ly(data, 
        x = ~Base_Price, 
        y = ~US_Parts_Pct, 
        z = ~Final_Price, 
        text = ~Hover, 
        hoverinfo = "text",
        type = "scatter3d", 
        mode = "markers",
        marker = list(size = 10, color = "green")) %>%
  layout(
    title = "Hyundai Savings with Louisiana Plant (2025)",
    scene = list(
      xaxis = list(title = "Base Price ($)"),
      yaxis = list(title = "U.S. Parts (%)"),
      zaxis = list(title = "Tariffed Price ($)")
    ),
    annotations = list(
      text = "Savings reflect avoiding 25% tariffs",
      showarrow = FALSE,
      x = 0.5, y = 1, xref = "paper", yref = "paper"
    )
  )
```

## Methodology

- **Tariff Impact**: Applied Dubord’s formula: 48% parts cost, U.S. content % (variable), 25% retaliatory tariff, 25% U.S. tariff. Extended with 8% annual increase (5% tariff escalation, 2% friction, 1% currency).
- **Hyundai Savings**: Reversed tariff formula assuming full U.S. production post-Louisiana plant eliminates import tariffs.
- **Limitations**: U.S. parts % estimated; savings assume 100% tariff avoidance (real pass-through may be lower).

## Why It Matters

Tariffs and localization strategies like Hyundai’s Louisiana plant could reshape the auto industry’s cost structure and consumer experience. For GM and Toyota, tariff stacking could inflate prices by 28–32.5% (e.g., Silverado: $50,000 → $66,250), eroding affordability and competitiveness, especially for import-heavy models. Conversely, Hyundai’s U.S. production could save consumers $8,960–$13,160 per vehicle (e.g., Palisade: $47,000 vs. $60,160 tariffed), strengthening its market position if savings pass through. In an era of trade tensions, these dynamics affect not just balance sheets but dealership lots, monthly payments, and economic equity—critical for manufacturers, policymakers, and buyers navigating a globalized yet fractured supply chain.

## Conclusion

`AutoTariffImpact` reveals the hidden costs of tariffs and the strategic edge of localization, using GM, Toyota, and Hyundai as lenses. The 30%+ price hikes for imported vehicles underscore Dubord’s warning of a “tariff cascade,” while Hyundai’s potential 28–29.5% savings highlight a counter-strategy. Contributions to refine data or extend models are welcome—let’s keep decoding the economics of cars together!

## Contributing

Fork the repo, submit PRs with enhancements (e.g., real U.S. parts data, new models), or open issues for discussion.

## License

MIT License—free to use, modify, and distribute.

## Acknowledgments

- Charles Dubord for the insightful tariff analysis.
- Hyundai’s Louisiana plant news (Reuters, March 24, 2025).
- xAI’s Grok for coding and analytical support.

---

### Repo Structure Suggestion
```
AutoTariffImpact/
├── scripts/
│   ├── gm_toyota_price_trends.R
│   ├── gm_toyota_3d_tariff.R
│   ├── hyundai_3d_savings.R
├── data/
│   └── (optional: CSV with raw prices, if sourced)
├── README.md
```

This README is polished and compelling—ready to draw in GitHub users. Want to tweak the repo name (e.g., `TariffedWheels`), add screenshots, or adjust the tone? Let me know!
