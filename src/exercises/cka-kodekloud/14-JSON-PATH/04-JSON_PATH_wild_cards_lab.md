# JSON PATH Wild Cards

#### Reference

[JSON Wild Cards](https://notes.kodekloud.com/docs/JSON-Path-Test-Free-Course/Lesson/JSON-PATH-2-Wild-Cards)

You don’t learn to walk by following rules. You learn by doing, and falling over.

– Richard Branson

You don't get what you wish for. You get what you work for.

– Daniel Milstein


1. Develop a JSON PATH query to extract the kind of object. A file named q1.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "value1",
  "value2"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q1.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer1.sh in /root directory.


```sh
echo "cat q1.json | jpath '$.*'" > answer1.sh
cat q1.json | jpath '$.*' >> answer1.sh 
```

**answer1.sh**

```bash
cat q1.json | jpath '$.*'
[
  "value1",
  "value2"
]
```

2. Develop a JSON PATH query to extract the kind of object. A file named q2.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "blue",
  "white"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q2.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer2.sh in /root directory.


```bash
 echo "cat q2.json | jpath '$.*.color'" > answer2.sh
 cat q2.json | jpath '$.*.color' >> answer2.sh 
```

**answer2.sh**

```sh
cat q2.json | jpath '$.*.color'
[
  "blue",
  "white"
]
```

3. Develop a JSON PATH query to extract the kind of object. A file named q3.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "$20,000",
  "$120,000"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q3.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer3.sh in /root directory.

```bash
echo "cat q3.json | jpath '$.vehicles.*.price'" > answer3.sh
cat q3.json | jpath '$.vehicles.*.price' >> answer3.sh
```

**answer3.sh**

```sh
cat q3.json | jpath '$.vehicles.*.price'
[
  "$20,000",
  "$120,000"
]
```

4. Develop a JSON PATH query to extract the kind of object. A file named q4.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "KDJ39848T",
  "MDJ39485DK",
  "KCMDD3435K",
  "JJDH34234KK"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q4.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer4.sh in /root directory.

```bash
echo "cat q4.json | jpath '$.*.model'" > answer4.sh
cat q4.json | jpath '$.*.model' >> answer4.sh
```


**answer4.sh**

```sh
cat q4.json | jpath '$.*.model'
[
  "KDJ39848T",
  "MDJ39485DK",
  "KCMDD3435K",
  "JJDH34234KK"
]
```

5. Develop a JSON PATH query to extract the kind of object. A file named q5.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "KDJ39848T",
  "MDJ39485DK",
  "KCMDD3435K",
  "JJDH34234KK"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q5.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer5.sh in /root directory.


```bash
echo "cat q5.json | jpath '$.car.wheels.*.model'" > answer5.sh
 cat q5.json | jpath '$.car.wheels.*.model' >> answer5.sh
```


**answer5.sh**

```sh
cat q5.json | jpath '$.car.wheels.*.model'
[
  "KDJ39848T",
  "MDJ39485DK",
  "KCMDD3435K",
  "JJDH34234KK"
]
```

6. Develop a JSON PATH query to extract the kind of object. A file named q6.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "KDJ39848T",
  "MDJ39485DK",
  "KCMDD3435K",
  "JJDH34234KK",
  "BBBB39848T",
  "BBBB9485DK",
  "BBBB3435K",
  "BBBB4234KK"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q6.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer6.sh in /root directory.


```bash
echo "cat q6.json | jpath '$.*.wheels[*].model'" > answer6.sh
cat q6.json | jpath '$.*.wheels[*].model' >> answer6.sh
```

**answer6.sh**

```sh
cat q6.json | jpath '$.*.wheels[*].model'
[
  "KDJ39848T",
  "MDJ39485DK",
  "KCMDD3435K",
  "JJDH34234KK",
  "BBBB39848T",
  "BBBB9485DK",
  "BBBB3435K",
  "BBBB4234KK"
]
```

7. Develop a JSON PATH query to extract the kind of object. A file named q7.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  1400,
  2400,
  3400
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q7.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer7.sh in /root directory.


```bash
echo "cat q7.json | jpath '$.employee.payslips.*.amount'" > answer7.sh
cat q7.json | jpath '$.employee.payslips.*.amount' >> answer7.sh 
```

**answer7.sh**

```sh
cat q7.json | jpath '$.employee.payslips.*.amount'
[
  1400,
  2400,
  3400
]
```

8. Develop a JSON PATH query to extract the kind of object. A file named q8.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "Arthur",
  "Gérard",
  "Donna",
  "Frances H.",
  "George P.",
  "Sir Gregory P.",
  "James P.",
  "Tasuku",
  "Denis",
  "Nadia",
  "William D.",
  "Paul M.",
  "Kailash",
  "Malala",
  "Rainer",
  "Barry C.",
  "Kip S.",
  "Jacques",
  "Joachim",
  "Richard",
  "Jeffrey C.",
  "Michael",
  "Michael W."
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q8.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer8.sh in /root directory.


```bash
echo "cat q8.json | jpath $.prizes[*].laureates[*].firstname" > answer8.sh
cat q8.json | jpath $.prizes[*].laureates[*].firstname >> answer8.sh
```

**answer8.sh**

```sh
cat q8.json | jpath $.prizes[*].laureates[*].firstname
[
  "Arthur",
  "Gérard",
  "Donna",
  "Frances H.",
  "George P.",
  "Sir Gregory P.",
  "James P.",
  "Tasuku",
  "Denis",
  "Nadia",
  "William D.",
  "Paul M.",
  "Kailash",
  "Malala",
  "Rainer",
  "Barry C.",
  "Kip S.",
  "Jacques",
  "Joachim",
  "Richard",
  "Jeffrey C.",
  "Michael",
  "Michael W."
]
```

9. Develop a JSON PATH query to extract the kind of object. A file named q9.json is provided in the terminal. Your task is to develop a JSON path query to extract the expected output from the Source Data.

Expected output should be like this

[
  "Kailash",
  "Malala"
]

You can test the JSON PATH query as below (noted that the query below is just sample, not a solution).

cat q9.json | jpath $.property1
[
  "value1"
]

If the output matched the expectation then write the whole command into a file named answer9.sh in /root directory.


Note: Remember to enclose your actual query in single-quotes:
cat q9.json | jpath '<query>'

Not doing so may cause the validation to fail.


```bash
echo "cat q9.json | jpath '$.prizes[?(@.year == 2014)].laureates[*].firstname'" > answer9.sh
cat q9.json | jpath '$.prizes[?(@.year == 2014)].laureates[*].firstname' >> answer9.sh 
```

**answer9.sh**

```sh
cat q9.json | jpath '$.prizes[?(@.year == 2014)].laureates[*].firstname'
[
  "Kailash",
  "Malala"
]
```



