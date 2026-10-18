# Outcomes: NYC Airbnb market segments

Course context: DS 514 Data Mining, *Unveiling Airbnb NYC: Data-Driven Recommendations via Clustering*.

The study covers a little over 100,000 New York City listings and 26 attributes. The goal is to describe how supply, price, and location sit together, then recommend where a supply strategy would change the business picture.

## Observation

Supply is concentrated, and the scarce room type is the expensive one.

- Entire homes and private rooms are the market. Entire home/apt is 53,699 listings (52.34%). Private rooms are 46,550 (45.37%). Shared rooms are 2.17%. Hotel rooms are 116 listings, or 0.11%.
- Hotel rooms have the highest average price at 668.47. Shared rooms average 634.10. Entire homes average 625.08 and private rooms average 625.03. Entire homes are not cheaper than private rooms in this snapshot. They are essentially the same price, and both sit below shared rooms and hotel rooms.
- Manhattan (42.69%) and Brooklyn (40.80%) hold about 83% of listings. Queens is 12.93%. The Bronx is 2.64% and Staten Island is 0.93%.
- Borough average prices for the main labels are close: Queens 630.23, Bronx 627.76, Brooklyn 626.59, Staten Island 624.49, Manhattan 622.46. A leftover label, `The Bronx`, averages 909. That label is too thin to treat as the borough price.
- Spelling variants (`brookln`, `manhatan`, `Queens County`) show that neighbourhood-group text was not fully standardized before aggregation.
- Price has a broad, roughly symmetric spread in the box plot used in the study, without a separate outlier class that would dominate the mean.
- The correlation view among price, minimum nights, number of reviews, and availability does not show one variable driving the others. Segments need more than a single feature.
- Missing neighbourhood text was filled from coordinates. Most other gaps were filled with a mean, a mode, or a placeholder such as "No Date" or "no license". License is missing for nearly every row, so it is not a useful business field in this file.

## Analysis

K-means was fit on price, service fee, minimum nights, number of reviews, latitude, and longitude. The elbow plot supported **three clusters**. The silhouette score is **0.551**, which is a moderate separation: the groups are usable, not sharply isolated.

What the three clusters have in common matters as much as what separates them.

| Cluster | Most common neighbourhood | Lowest price in cluster | Highest price in cluster | Average availability (days) | Average reviews per month |
| --- | --- | ---: | ---: | ---: | ---: |
| 0 | Bedford-Stuyvesant | 429 (Pelham Gardens, Bronx) | 823 (Financial District, Manhattan) | 140 | 1.17 |
| 1 | Bedford-Stuyvesant | 50 (Washington Heights, Manhattan) | 433 (Upper West Side, Manhattan) | 141 | 1.15 |
| 2 | Williamsburg | 818 (South Slope, Brooklyn) | 1,200 (Harlem, Manhattan) | 141 | 1.17 |

Reading those rows carefully:

- The popular neighbourhoods inside the clusters are Brooklyn names: Bedford-Stuyvesant and Williamsburg. The extreme prices, especially the maximums, sit in Manhattan (Financial District, Upper West Side, Harlem) and in a high Brooklyn listing (South Slope).
- Availability and review velocity barely differ across clusters. Guests are not leaving one segment idle while another turns over much faster. The split is about price, fees, and place, not about a review or occupancy gap.
- Service fees track the same geography. Cluster maximum fees land in Bedford-Stuyvesant (239), East Harlem (imputed mean near 125), and Chinatown (240). Cluster minimum fees of 10 appear in Morningside Heights and Clinton Hill.
- High listing share and high price coexist in Manhattan and Brooklyn. The course report treats that pattern as evidence that scarce local supply inside already busy boroughs is tied to higher prices. The borough totals show those two boroughs are not short of listings overall. The pressure shows up inside neighbourhoods: Harlem at 1,200, the Financial District at 823, South Slope at 818.
- Queens, the Bronx, and Staten Island remain the thin part of the map. They are not where the cluster price peaks sit.

The map of cluster markers is a spatial check on the same three groups. It does not create a fourth segment.

## Recommendation

The DS 514 report recommendation is the decision this study is willing to defend:

**Add listings in the New York neighbourhoods where prices are already the highest, rather than treating Manhattan and Brooklyn as markets that are finished.**

The report states that neighbourhoods such as Brooklyn and Manhattan show the highest minimum and maximum prices, and that limited presence in those pockets is associated with higher prices. Expanding inventory there is the proposed way to narrow the price gap and grow the NYC business.

How to act on that, given the tables above:

1. **Prioritize neighbourhoods that produced the price peaks, not a blanket borough campaign.** Harlem, the Financial District, the Upper West Side, and South Slope are the listings at the top of each cluster. Bedford-Stuyvesant and Williamsburg are the neighbourhoods that already define cluster membership and are the natural places to add comparable supply.
2. **Do not chase hotel-room inventory as a growth bet.** Hotel rooms are 0.11% of supply and the highest average price. They are a luxury tail. The volume business is entire homes and private rooms, which are priced almost identically near 625.
3. **Keep Queens, the Bronx, and Staten Island as a coverage market, not the first expansion bet.** Together they are under 17% of listings, but their borough averages are close to Manhattan and Brooklyn. The sharp premiums in this file are inside Manhattan and Brooklyn neighbourhoods.
4. **Clean borough labels before the next pricing model.** `The Bronx`, `brookln`, `manhatan`, and `Queens County` should be mapped to the five official boroughs. Until that is done, borough averages overstate a few thin groups.
5. **Treat the three clusters as pricing bands, not as different guest products.** Availability near 140 days and reviews per month near 1.16 are shared. A host tool can price from cluster plus neighbourhood. It should not assume one cluster is "high demand" and another is "low demand" on reviews alone.
6. **Revisit the silhouette before automating the segments.** A score of 0.55 supports a three-way story for this course analysis. A production model should re-check K on a refreshed extract and should drop or cap imputed service-fee values so a filled mean (about 125) is not read as a real fee.

## Limits

- One snapshot. Nothing here measures a price change after new listings are added, so the supply-and-price link is an association from this file, not a measured cause.
- Means were used to fill missing prices and service fees. Those filled values appear in some cluster "minimum" and "maximum" fee rows and should not be quoted as observed fees.
- Nominatim results depend on an external geocoder and can return a suburb name that does not match Airbnb's borough list.
- The public map of every point is illustrative. Decisions in this document use the tables, not the marker density.
