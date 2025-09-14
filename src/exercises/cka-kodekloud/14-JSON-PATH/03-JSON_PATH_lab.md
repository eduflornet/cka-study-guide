# JSON PATH Lab

Always bear in mind that your own resolution to success is more important than any other one thing.

– Abraham Lincoln

1. Develop a JSON PATH query to extract the kind of object. A file named **q1.json** is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

```json
[
  "value1"
]
```

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

```bash
cat q1.json | jpath '$.property1'
[
  "value1"
]
```

If the output matched the expectation then write the whole command into a file named answer1.sh in the /root directory.

2. Develop a JSON PATH query to extract the kind of object. A file named **q2.json** is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

```json
[
  {
    "color": "white",
    "price": "$120,000"
  }
]
```

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

```bash
cat q2.json | jpath '$.property1'
[
  "value1"
]
```

If the output matched the expectation then write the whole command into a file named **answer2.sh** in the /root directory.

```bash
cat q2.json | jpath '$.bus'
[
  {
    "color": "white",
    "price": "$120,000"
  }
]
```

3. Develop a JSON PATH query to extract the kind of object. A file named **q3.json** is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

```json
[
  "$120,000"
]
```

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

```bash
cat q3.json | jpath '$.property1'
[
  "value1"
]
```

If the output matched the expectation then write the whole command into a file named answer3.sh in the /root directory.

**Answer3.sh**

```sh
cat q3.json | jpath '$.bus.price'
[
  "$120,000"
]
```

4. Develop a JSON PATH query to extract the kind of object. A file named q4.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "$20,000"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q4.json | jpath '$.property1'
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer4.sh in the /root directory.

**Answer4.sh**

```sh
cat q4.json | jpath '$.vehicles.car.price'
[
  "$20,000"
]

```

5. Develop a JSON PATH query to extract the kind of object. A file named **q5.json** is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

```json
[
    [
        {
            "model": "KDJ39848T",
            "location": "front-right"
        },
        {
            "model": "MDJ39485DK",
            "location": "front-left"
        },
        {
            "model": "KCMDD3435K",
            "location": "rear-right"
        },
        {
            "model": "JJDH34234KK",
            "location": "rear-left"
        }
    ]
]
```

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

```bash
cat q5.json | jpath '$.property1'
[
  "value1"
]
```

If the output matched the expectation then write the whole command into a file named answer5.sh in the /root directory.

**Answer5.sh**

```sh
 cat q5.json | jpath '$.car.wheels'
[
  [
    {
      "model": "KDJ39848T",
      "location": "front-right"
    },
    {
      "model": "MDJ39485DK",
      "location": "front-left"
    },
    {
      "model": "KCMDD3435K",
      "location": "rear-right"
    },
    {
      "model": "JJDH34234KK",
      "location": "rear-left"
    }
  ]
]
```

6. Develop a JSON PATH query to extract the kind of object. A file named q6.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  {
    "model": "KCMDD3435K",
    "location": "rear-right"
  }
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q6.json | jpath '$.property1'
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer6.sh in the /root directory.


**Answer6.sh**

```sh
cat q6.json | jpath '$.car.wheels[2]'
[
  {
    "model": "KCMDD3435K",
    "location": "rear-right"
  }
]
```

7. Develop a JSON PATH query to extract the kind of object. A file named q7.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

```json
[
  "KCMDD3435K"
]
```

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

```bash
cat q7.json | jpath '$.property1'
[
  "value1"
]
```

If the output matched the expectation then write the whole command into a file named answer7.sh in the /root directory.


Note: Not using single quotes around your actual query would cause shell misinterpretation resulting in wrong answers.

**Answer7.sh**


```sh
cat q7.json | jpath '$.car.wheels[2].model'
[
  "KCMDD3435K"
]

```

8. Develop a JSON PATH query to extract the kind of object. A file named q8.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  [
    {
      "month": "june",
      "amount": 1400
    },
    {
      "month": "july",
      "amount": 2400
    },
    {
      "month": "august",
      "amount": 3400
    }
  ]
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q8.json | jpath '$.property1'
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer8.sh in the /root directory.


**Answer8.sh**


```sh
cat q8.json | jpath '$.employee.payslips'
[
  [
    {
      "month": "june",
      "amount": 1400
    },
    {
      "month": "july",
      "amount": 2400
    },
    {
      "month": "august",
      "amount": 3400
    }
  ]
]

```

9. Develop a JSON PATH query to extract the kind of object. A file named q9.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  {
    "month": "august",
    "amount": 3400
  }
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q9.json | jpath '$.property1'
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer9.sh in the /root directory.


**Answer9.sh**

```sh
cat q9.json | jpath '$.employee.payslips[2]'
[
  {
    "month": "august",
    "amount": 3400
  }
]

```

10. Develop a JSON PATH query to extract the kind of object. A file named q10.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  3400
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q10.json | jpath '$.property1'
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer10.sh in the /root directory.

**Answer10.sh**

```sh
cat q10.json | jpath '$.employee.payslips[2].amount'
[
  3400
]

```

11. Develop a JSON PATH query to extract the kind of object. A file named q11.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  {
    "id": "914",
    "firstname": "Malala",
    "surname": "Yousafzai",
    "motivation": "\"for their struggle against the suppression of children and young people and for the right of all children to education\"",
    "share": "2"
  }
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q11.json | jpath '$.property1'
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer11.sh in the /root directory.

**Answer11.sh**

```sh

```





