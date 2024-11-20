---
title: Per județ
---

 

```sql alegeri
SELECT * FROM (
  VALUES 
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

```sql ziData
SELECT  alegeri, date, time, ${inputs.liste.value} AS voturi
FROM Judete
WHERE alegeri IN ${inputs.alegeri.value} AND Judet = COALESCE('${inputs.judete.value}', Judet)
ORDER BY time ASC;
```

```sql ziDataJud
SELECT -- to DELETE
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

WHERE alegeri == '${inputs.alegeriSingle.value}' AND Judet == '${inputs.judete.value}'
    AND voturi > 0
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
WHERE alegeri == '${inputs.alegeriSingle.value}' AND Judet == '${inputs.judete.value}' AND value IS NOT NULL; -- Optional: Filter out rows with NULL values

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
WHERE alegeri IN ${inputs.alegeri.value} AND Judet == '${inputs.judete.value}' AND voturi IS NOT NULL
ORDER BY time ASC;

```

## Comparații alegeri

<Dropdown
    data={judete} 
    name=judete
    value=Judet
    title='Judet'
    xnoDefault = true
    defaultValue={['B']}
/>

<Dropdown
    data={alegeri} 
    name=alegeri
    value=id
    title='Alegeri'
    label=label
    multiple=true
    defaultValue={['11122016-2016-parl', '6122020-2020-parl']}	
/>

<Dropdown
    data={liste} 
    name=liste
    value=lista
    label=label
    title='Voturi'
    defaultValue={['LP']}
/>

<!-- Selected: <br/> {inputs.alegeri.value} <br/>  {inputs.judete.value} <br/>  {inputs.liste.value} -->
Județ: <b>{inputs.judete.value}</b> / tip listă: <b>{inputs.liste.value} </b>

### Cumulativ

<LineChart data={ziData} x="time" y="voturi" series="alegeri" title="Prezență vot în timp - agregat (cumulativ)" xLabel="Timp" yLabel="Voturi"  sort=True />

### Pe oră

<LineChart data={ziDataDiff} x="time" y="voturi_diferenta" series="alegeri" title="Voturi {inputs.liste.label}" xLabel="Timp" yLabel="Voturi"  sort=True     type=grouped />

----

## Demografice

<Dropdown
    data={alegeri} 
    name=alegeriSingle
    value=id
    title='Alegeri Single'
    label=label
    defaultValue={['6122020-2020-parl']}	
/>

<BarChart data={nationalDemographics} x="time" y="difference" series="category" title="Demografie" xLabel="Timp" yLabel="Voturi"  sort=True />



 <!-- <Heatmap 
      data={ziDataJud} 
      x=time 
      y="Judet" 
      value=diferenta   
      legend=true
      filter=true
      title="voturi / oră"            
  /> -->