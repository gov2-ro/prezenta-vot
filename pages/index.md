---
title: Prezență vot Național
---

 

```sql alegeri
SELECT * FROM (
  VALUES 
('2024-prez-1', '2024 Prez 1'),
('2019-euparl', '2019 Euparl'),
('2019-ref', '2019 Ref'),
('2024-euparl', '2024 Euparl'),
('2024-local', '2024 Local'),
('2016-parl', '2016 Parl'),
('2019-prez-1', '2019 Prez-1'),
-- ('2019-prez-2', '2019 Prez-2'),
('2020-parl', '2020 Parl')
) AS t(id, label)
```

```sql judete
  select DISTINCT Judet from Judete where diaspora = ${inputs.ro_diaspora.value}
  -- UNION
  -- SELECT '' AS Judet
 
``` 

```sql liste
SELECT * FROM (
  VALUES 
    ('LP', 'Liste permanente'),
    ('LS', 'Liste suplimentare'),
    -- ('LSC', 'Liste Supl Complementare'),
    ('UM', 'Urnă mobilă')
    -- ('LT', 'Liste t?')
) AS t(lista, label)

``` 

```sql ro_diaspora
SELECT * FROM (
  VALUES 
    (0, 'România'),
    (1, 'diaspora')
    
) AS t(id, label)

``` 

```sql ziDataJud

SELECT 
    Judet,
       CAST(substr(timestamp, 12, 5) AS TEXT) AS timestamp,
    LP AS voturi 
FROM Judete
WHERE alegeri == '2024-prez-1' and diaspora = 0
    AND voturi > 0
ORDER BY timestamp ASC;
```
 
 
```sql ziData
SELECT  alegeri,  timestamp , ${inputs.liste.value} AS voturi
FROM National
WHERE alegeri IN ${inputs.alegeri.value}   and diaspora = ${inputs.ro_diaspora.value}
ORDER BY alegeri, timestamp ASC;
-- SELECT  alegeri,  timestamp,  sum(LS) AS voturi
-- FROM National
-- WHERE alegeri = '2024-prez-1'
-- group by  alegeri, timestamp
-- ORDER BY timestamp ASC;
```

 
```sql nationalDemographics

   WITH aggregated_data AS (
    SELECT
     
        timestamp,
        SUM(M_1824) AS M_1824,
        SUM(M_2534) AS M_2534,
        SUM(M_3544) AS M_3544,
        SUM(M_4564) AS M_4564,
        SUM("M_65+") AS "M_65+",
        SUM(F_1824) AS F_1824,
        SUM(F_2534) AS F_2534,
        SUM(F_3544) AS F_3544,
        SUM(F_4564) AS F_4564,
        SUM("F_65+") AS "F_65+"
    FROM judete
    WHERE diaspora = ${inputs.ro_diaspora.value}  AND alegeri == '${inputs.alegeriSingle.value}'
      AND (M_1824 > 0 OR M_2534 > 0 OR M_3544 > 0 OR M_4564 > 0 OR "M_65+" > 0
           OR F_1824 > 0 OR F_2534 > 0 OR F_3544 > 0 OR F_4564 > 0 OR "F_65+" > 0)
    GROUP BY timestamp 
)
SELECT
    timestamp,
    'F_1824' AS category,
    SUM(F_1824) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_2534' AS category,
    SUM(F_2534) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_3544' AS category,
    SUM(F_3544) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_4564' AS category,
    SUM(F_4564) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_65+' AS category,
    SUM("F_65+") AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_1824' AS category,
    SUM(M_1824) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_2534' AS category,
    SUM(M_2534) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_3544' AS category,
    SUM(M_3544) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_4564' AS category,
    SUM(M_4564) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_65+' AS category,
    SUM("M_65+") AS value
FROM aggregated_data
GROUP BY timestamp;



```

```sql nationalDemographicsFemales


   WITH aggregated_data AS (
    SELECT
     
        timestamp,
        SUM(F_1824) AS F_1824,
        SUM(F_2534) AS F_2534,
        SUM(F_3544) AS F_3544,
        SUM(F_4564) AS F_4564,
        SUM("F_65+") AS "F_65+",
       
    FROM judete
    WHERE diaspora = ${inputs.ro_diaspora.value}  AND alegeri == '${inputs.alegeriSingle.value}'
      AND (F_1824 > 0 OR F_2534 > 0 OR F_3544 > 0 OR F_4564 > 0 OR "F_65+" > 0
          )
    GROUP BY timestamp 
)
SELECT
    timestamp,
    'F_1824' AS category,
    SUM(F_1824) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_2534' AS category,
    SUM(F_2534) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_3544' AS category,
    SUM(F_3544) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_4564' AS category,
    SUM(F_4564) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'F_65+' AS category,
    SUM("F_65+") AS value
FROM aggregated_data
GROUP BY timestamp;




```

```sql nationalDemographicsMales


   WITH aggregated_data AS (
    SELECT
     
        timestamp,
        SUM(M_1824) AS M_1824,
        SUM(M_2534) AS M_2534,
        SUM(M_3544) AS M_3544,
        SUM(M_4564) AS M_4564,
        SUM("M_65+") AS "M_65+",
       
    FROM judete
    WHERE diaspora = ${inputs.ro_diaspora.value}  AND alegeri = '${inputs.alegeriSingle.value}'
      AND (M_1824 > 0 OR M_2534 > 0 OR M_3544 > 0 OR M_4564 > 0 OR "M_65+" > 0
          )
    GROUP BY timestamp 
)
SELECT
    timestamp,
    'M_1824' AS category,
    SUM(M_1824) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_2534' AS category,
    SUM(M_2534) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_3544' AS category,
    SUM(M_3544) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_4564' AS category,
    SUM(M_4564) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'M_65+' AS category,
    SUM("M_65+") AS value
FROM aggregated_data
GROUP BY timestamp;


```

```sql voturi_liste

WITH aggregated_data AS (
    SELECT 
        timestamp,
        SUM(LP) AS LP, 
        SUM(LS) AS LS, 
        SUM(UM) AS UM
 FROM national
WHERE alegeri == '${inputs.alegeriSingle.value}' AND diaspora = ${inputs.ro_diaspora.value} AND (LP > 0 OR LS > 0 OR UM > 0)
group by timestamp
)
select
    timestamp,
    'LP' AS category,
    SUM(LP) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'LS' AS category,
    SUM(LS) AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'UM' AS category,
    SUM(UM) AS value
FROM aggregated_data
GROUP BY timestamp;

```

```sql mfs

WITH aggregated_data AS (
    SELECT 
        timestamp,
        SUM(M_1824) AS M_1824,
        SUM(M_2534) AS M_2534,
        SUM(M_3544) AS M_3544,
        SUM(M_4564) AS M_4564,
        SUM("M_65+") AS "M_65+",
        SUM(F_1824) AS F_1824,
        SUM(F_2534) AS F_2534,
        SUM(F_3544) AS F_3544,
        SUM(F_4564) AS F_4564,
        SUM("F_65+") AS "F_65+"
    FROM national
    WHERE diaspora = ${inputs.ro_diaspora.value}  AND alegeri == '${inputs.alegeriSingle.value}'
      AND (M_1824 > 0 OR M_2534 > 0 OR M_3544 > 0 OR M_4564 > 0 OR "M_65+" > 0
           OR F_1824 > 0 OR F_2534 > 0 OR F_3544 > 0 OR F_4564 > 0 OR "F_65+" > 0)
    GROUP BY timestamp
)
SELECT
    timestamp,
    'Males' AS category,
    SUM(M_1824 + M_2534 + M_3544 + M_4564 + "M_65+") AS value
FROM aggregated_data
GROUP BY timestamp
UNION ALL
SELECT
    timestamp,
    'Females' AS category,
    SUM(F_1824 + F_2534 + F_3544 + F_4564 + "F_65+") AS value
FROM aggregated_data
GROUP BY timestamp;


```

## Dinamică / ore

<Dropdown
    data={ro_diaspora}
    name=ro_diaspora
    value=id
    title='România/Diaspora'
    label=label
 
    defaultValue={[0]}	
/>


<!-- <Dropdown
    data={alegeri} 
    name=alegeri
    value=id
    title='Alegeri'
    label=label
    multiple=true
    defaultValue={['2024-prez-1']}	
/> -->
<!-- ,'10112019-2019-prez-1','24112019-2019-prez-2' -->
 
 <Dropdown
    data={alegeri} 
    name=alegeriSingle
    value=id
    title='Alegeri'
    label=label
    defaultValue={['2024-prez-1']}	
/>

<!-- <Dropdown
    data={liste} 
    name=liste
    value=lista
    label=label
    title='Voturi'
    defaultValue={['LS']}
/> -->

<!-- Selected: <br/> {inputs.alegeri.value} <br/>  {inputs.judete.value} <br/>  {inputs.liste.value} -->
 
   

<!-- <BarChart data={ziData} x="timestamp" y="voturi" series="alegeri" title="Voturi {inputs.liste.label} - absolut (cumulativ)" xLabel="Timp" yLabel="Voturi"  sort=True /> -->


<!-- <BarChart data={ziDataDiff} x="time" y="voturi_diferenta" series="alegeri" title="Voturi {inputs.liste.label}" xLabel="Timp" yLabel="Voturi"  sort=True     type=grouped /> -->
<!-- <LineChart data={voturi_liste} x="timestamp" y="value" series="category" title="Voturi x tip listă" xLabel="Timp" yLabel="Voturi"  sort=True     type=grouped /> -->
<BarChart data={voturi_liste} x="timestamp" y="value" series="category" title="Voturi x tip listă" xLabel="Timp" yLabel="Voturi"  sort=True      />

----

## Demografice



<!-- <BarChart data={nationalDemographics} x="timestamp" y="value" series="category" title="Demografie" xLabel="Timp" yLabel="Voturi"  sort=True /> -->
<LineChart data={nationalDemographicsMales} x="timestamp" y="value" series="category" title="Bărbați" xLabel="Timp" yLabel="Voturi"  sort=True />
<LineChart data={nationalDemographicsFemales} x="timestamp" y="value" series="category" title="Femei" xLabel="Timp" yLabel="Voturi"  sort=True />
<LineChart data={mfs} x="timestamp" y="value" series="category" title="♀ x ♂" xLabel="Timp" yLabel="Voturi"  sort=True />

### Heatmap
 
 <Heatmap 
      data={ziDataJud} 
      x=timestamp 
      y="Judet" 
      value=voturi   
      legend=true
      filter=true
      title="voturi / oră"            
  /> 


 