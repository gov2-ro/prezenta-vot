---
title: Prezență vot Național
---

 

```sql alegeri
SELECT * FROM (
  VALUES 
('22112024-2024-prez-1.1', '2024 Prez 1.1 Diaspora'),
('26052019-2019-euparl', '2019 Euparl'),
('26052019-2019-ref', '2019 Ref'),
('9062024-2024-euparl', '2024 Euparl'),
('9062024-2024-local', '2024 Local'),
('11122016-2016-parl', '2016 Parl'),
('10112019-2019-prez-1', '2019 Prez-1'),
('24112019-2019-prez-2', '2019 Prez-2'),
('6122020-2020-parl', '2020 Parl')
) AS t(id, label)
```

```sql judete
  select DISTINCT Judet from Judete
  -- UNION
  -- SELECT '' AS Judet
 
``` 

```sql liste
SELECT * FROM (
  VALUES 
    ('LP', 'Liste permanente'),
    ('LS', 'Liste suplimentare'),
    ('LSC', 'Liste Supl Complementare'),
    ('UM', 'Urnă mobilă'),
    ('LT', 'Liste t?')
) AS t(lista, label)

``` 

```sql ziDataJud
SELECT 
    Judet,
    alegeri,
    date,
    time,
    ${inputs.liste.value} AS voturi,
    ${inputs.liste.value} - LAG(${inputs.liste.value}, 1) OVER (
        PARTITION BY Judet, alegeri 
        ORDER BY time
    ) as diferenta
FROM Judete
WHERE alegeri == '${inputs.alegeriSingle.value}'
    AND voturi > 0
ORDER BY time ASC;
```

```sql ziData
SELECT  alegeri, date, time, ${inputs.liste.value} AS voturi
FROM National
WHERE alegeri IN ${inputs.alegeri.value} 
ORDER BY time ASC;
```

```sql ziDataDiff

SELECT  
    alegeri, 
    date, 
    time, 
    ${inputs.liste.value} AS voturi,
    ${inputs.liste.value} - LAG(${inputs.liste.value}, 1) OVER (
        PARTITION BY alegeri, Judet 
        ORDER BY time
    ) AS voturi_diferenta
FROM Judete
WHERE alegeri IN ${inputs.alegeri.value} 
 
ORDER BY time ASC;


```


```sql nationalDemographics

SELECT 
    alegeri,
    Judet,
    date,
    time,
    category,
    value,
    value - LAG(value, 1) OVER (
        PARTITION BY alegeri, Judet, category
        ORDER BY time
    ) AS difference
FROM Judete
UNPIVOT (
    value FOR category IN (
        M_1824,
        M_2534,
        M_3544,
        M_4564,
        'M_65+',
        F_1824,
        F_2534,
        F_3544,
        F_4564,
        'F_65+'
    )
)
WHERE alegeri == '${inputs.alegeriSingle.value}' AND value IS NOT NULL; -- Optional: Filter out rows with NULL values

```

## Dinamică / ore

<Dropdown
    data={alegeri} 
    name=alegeri
    value=id
    title='Alegeri'
    label=label
    multiple=true
    defaultValue={['22112024-2024-prez-1.1']}	
/>
<!-- ,'10112019-2019-prez-1','24112019-2019-prez-2' -->
 

<Dropdown
    data={liste} 
    name=liste
    value=lista
    label=label
    title='Voturi'
    defaultValue={['LS']}
/>

<!-- Selected: <br/> {inputs.alegeri.value} <br/>  {inputs.judete.value} <br/>  {inputs.liste.value} -->
   
### Cumulativ

<LineChart data={ziData} x="time" y="voturi" series="alegeri" title="Voturi {inputs.liste.label} - absolut (cumulativ)" xLabel="Timp" yLabel="Voturi"  sort=True />

### Pe oră

<BarChart data={ziDataDiff} x="time" y="voturi_diferenta" series="alegeri" title="Voturi {inputs.liste.label}" xLabel="Timp" yLabel="Voturi"  sort=True     type=grouped />

----

## Demografice

<Dropdown
    data={alegeri} 
    name=alegeriSingle
    value=id
    title='Alegeri Single'
    label=label
    defaultValue={['22112024-2024-prez-1.1']}	
/>

<BarChart data={nationalDemographics} x="time" y="difference" series="category" title="Demografie" xLabel="Timp" yLabel="Voturi"  sort=True />

### Heatmap

 <Heatmap 
      data={ziDataJud} 
      x=time 
      y="Judet" 
      value=diferenta   
      legend=true
      filter=true
      title="voturi / oră"            
  />


 