# JSON PATH List Lab

There is only one success - to be able to spend your life in your own way.

– Christopher Morley

1. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Apple"
]

Save the the query to filename answer1.sh under root directory.


```bash
echo "cat input.json | jpath $[0]" > answer1.sh
cat input.json | jpath $[0] >> answer1.sh 
```

**answer1.sh**

```sh
cat input.json | jpath 0
[
  "Apple"
]
```

2. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Apple"
  "Facebook"
]

Save the query to filename answer2.sh under the root directory.

```bash
echo "cat input.json | jpath '$[0,4]'" > answer2.sh
cat input.json | jpath '$[0,4]' >> answer2.sh 
```

**answer2.sh**

```sh
cat input.json | jpath '$[0,4]'
[
  "Apple",
  "Facebook"
]
```

3. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Apple",
  "Google",
  "Microsoft",
  "Amazon",
  "Facebook"
]

Save the query to filename answer3.sh under root directory.

```bash
echo "cat input.json | jpath '$[0,1,2,3,4]'" > answer3.sh
cat input.json | jpath '$[0,1,2,3,4]' >> answer3.sh
```

**answer3.sh**

```sh
cat input.json | jpath '$[0,1,2,3,4]'
[
  "Apple",
  "Google",
  "Microsoft",
  "Amazon",
  "Facebook"
]
```

4. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Microsoft",
  "Amazon",
  "Facebook",
  "Coca-Cola",
  "Samsung"
]

Save the query to filename answer4.sh under root directory.

```bash
echo "cat input.json | jpath '$[2,3,4,5,6]'" > answer4.sh
cat input.json | jpath '$[2,3,4,5,6]' >> answer4.sh 
```

**answer4.sh**

```sh
cat input.json | jpath '$[2,3,4,5,6]'
[
  "Microsoft",
  "Amazon",
  "Facebook",
  "Coca-Cola",
  "Samsung"
]
```

5. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "McDonald's"
]

Save the query to filename answer5.sh under root directory.

```bash
echo "cat input.json | jpath $[9]" > answer5.sh
cat input.json | jpath $[9] >> answer5.sh
```

**answer5.sh**

```sh
cat input.json | jpath $[9]
[
  "McDonald's"
]
```

6. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Samsung",
  "Disney",
  "Toyota",
  "McDonald's"
]

Save the query to filename answer6.sh under root directory.

```bash
echo "cat input.json | jpath '$[6,7,8,9]'" > answer6.sh
cat input.json | jpath '$[6,7,8,9]' >> answer6.sh 
```

**answer6.sh**

```sh
cat input.json | jpath '$[6,7,8,9]'
[
  "Samsung",
  "Disney",
  "Toyota",
  "McDonald's"
]
```

7. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Amazon",
  "Facebook",
  "Coca-Cola",
  "Samsung",
  "Disney",
  "Toyota"
]

Save the query to filename answer7.sh under root directory.

```bash
echo "cat input.json | jpath '$[3:9]'" > answer7.sh
cat input.json | jpath '$[3:9]' >> answer7.sh
```

**answer7.sh**

```sh
cat input.json | jpath '$[3:9]'
[
  "Amazon",
  "Facebook",
  "Coca-Cola",
  "Samsung",
  "Disney",
  "Toyota"
]
```

8. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

```json
# input.json
[
  "Curabitur Vel Lectus Limited",
  "Libero Morbi Accumsan Industries",
  "Faucibus Ltd",
  "Eu Corp.",
  "Neque Sed Corporation",
  "Nunc Commodo Incorporated",
  "Taciti Sociosqu Industries",
  "Rutrum Lorem Corp.",
  "Proin Corp.",
  "Dolor Fusce Corporation",
  "Malesuada Ut Sem LLC",
  "Mattis Ornare PC",
  "Pede Nonummy Ut LLP",
  "Aliquam PC",
  "Eu Consulting",
  "Leo Morbi Neque Incorporated",
  "Suspendisse Institute",
  "In Tincidunt Congue Consulting",
  "Ipsum Inc.",
  "Nulla Aliquet Proin Consulting",
  "Lorem Luctus Ut Consulting",
  "Sed Sapien Nunc Associates",
  "Feugiat Tellus Industries",
  "Sem LLP",
  "Aliquam Enim Nec Inc.",
  "Feugiat Tellus PC",
  "Dis Parturient Limited",
  "Sed Dictum Corporation",
  "Eu PC",
  "Tellus Faucibus Leo Corp.",
  "Velit Company",
  "Mauris Aliquam Eu Corp.",
  "Rutrum Justo Praesent Industries",
  "Malesuada Fames Associates",
  "Vitae Sodales Foundation",
  "Amet Company",
  "Dignissim Tempor Limited",
  "Morbi Tristique Corp.",
  "Nisi Cum Sociis Foundation",
  "Donec Vitae Erat Incorporated",
  "Fringilla Industries",
  "Elit Incorporated",
  "Velit In Institute",
  "Odio A Purus Incorporated",
  "Hendrerit Id Institute",
  "Aliquet Magna Associates",
  "Dictum Eu Corporation",
  "Integer Mollis Integer Corp.",
  "Libero Proin Mi Foundation",
  "Purus Sapien Associates",
  "Fringilla Associates",
  "Ante Company",
  "Bibendum Company",
  "Convallis Ligula Donec Industries",
  "Elit Inc.",
  "Scelerisque Foundation",
  "Curae; Corp.",
  "Ornare PC",
  "Ut Ipsum Consulting",
  "Tortor Ltd",
  "Convallis Dolor Quisque Foundation",
  "Feugiat Metus Sit Corp.",
  "Nec Orci Incorporated",
  "Arcu Vel Institute",
  "Diam Duis Corp.",
  "Ut Cursus Luctus Incorporated",
  "Vitae LLP",
  "Sed Sem Company",
  "Pede Cum Ltd",
  "Laoreet Lectus Foundation",
  "Semper Dui Foundation",
  "Odio A Purus Inc.",
  "Rutrum Magna Cras PC",
  "A Felis Company",
  "Libero Et Tristique Incorporated",
  "Odio Etiam Associates",
  "Cum Sociis Natoque Industries",
  "Nulla Dignissim Maecenas Inc.",
  "Malesuada Incorporated",
  "Lorem Eu Metus Foundation",
  "In Company",
  "Class Aptent Incorporated",
  "Ac Arcu Nunc Institute",
  "Aliquet Molestie LLP",
  "Sed LLC",
  "Pede LLP",
  "Ante Ipsum Primis Corporation",
  "Eu Dolor Ltd",
  "A Aliquet Consulting",
  "Lacinia Limited",
  "Pretium Aliquet Limited",
  "Magna Nec Corp.",
  "Egestas Corporation",
  "Est Congue Associates",
  "Non Cursus Inc.",
  "Elit Fermentum Associates",
  "Consectetuer Adipiscing Elit Limited",
  "Accumsan Convallis PC",
  "In Ltd"
]
```

The expected output should be like this.

[
  "In Ltd"
]

Save the query to filename answer8.sh under root directory.

```bash
echo "cat input.json | jpath '$[-1:]'" > answer8.sh
cat input.json | jpath '$[-1:]' >> answer8.sh
```

**answer8.sh**

```sh
cat input.json | jpath '$[-1:]'
[
  "In Ltd"
]
```

9. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Consectetuer Adipiscing Elit Limited",
  "Accumsan Convallis PC",
  "In Ltd"
]

Save the query to filename answer9.sh under root directory.


```bash
echo "cat input.json | jpath '$[-3:]'" > answer9.sh
cat input.json | jpath '$[-3:]' >> answer9.sh
```

**answer9.sh**

```sh
cat input.json | jpath '$[-3:]'
[
  "Consectetuer Adipiscing Elit Limited",
  "Accumsan Convallis PC",
  "In Ltd"
]
```

10. Develop a JSON path query to extract the expected output from the Source Data.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  "Magna Nec Corp.",
  "Egestas Corporation",
  "Est Congue Associates",
  "Non Cursus Inc.",
  "Elit Fermentum Associates",
  "Consectetuer Adipiscing Elit Limited"
]

Save the query to filename answer10.sh under root directory.

```bash
echo "cat input.json | jpath '$[-8:-2]'" > answer10.sh
cat input.json | jpath '$[-8:-2]' >> answer10.sh
```

**answer10.sh**

```sh
cat input.json | jpath '$[-8:-2]'
[
  "Magna Nec Corp.",
  "Egestas Corporation",
  "Est Congue Associates",
  "Non Cursus Inc.",
  "Elit Fermentum Associates",
  "Consectetuer Adipiscing Elit Limited"
]
```
11. Develop a JSON path query to extract the phone numbers of first 5 users.
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query


```json
# input.json
  {
    "age": 35,
    "name": "Tameka Lane",
    "gender": "female",
    "phone": "+1 (850) 469-2827"
  },
  {
    "age": 26,
    "name": "Kristy Day",
    "gender": "female",
    "phone": "+1 (825) 558-2599"
  },
  {
    "age": 36,
    "name": "Nieves Hill",
    "gender": "male",
    "phone": "+1 (946) 495-3285"
  },
  {
    "age": 30,
    "name": "Dianna Holland",
    "gender": "female",
    "phone": "+1 (948) 406-2941"
  },
  {
    "age": 23,
    "name": "Marsh Robertson",
    "gender": "male",
    "phone": "+1 (903) 413-2132"
  },
  {
    "age": 33,
    "name": "Valenzuela Mcbride",
    "gender": "male",
    "phone": "+1 (998) 499-2074"
  },
  {
    "age": 40,
    "name": "Virginia Michael",
    "gender": "female",
    "phone": "+1 (898) 505-3869"
  },
  {
    "age": 38,
    "name": "Mueller Keller",
    "gender": "male",
    "phone": "+1 (805) 555-3665"
  },
  {
    "age": 37,
    "name": "Madeline Farley",
    "gender": "female",
    "phone": "+1 (954) 446-2747"
  },
  {
    "age": 23,
    "name": "Potter Casey",
    "gender": "male",
    "phone": "+1 (948) 538-3644"
  },
  {
    "age": 24,
    "name": "Melinda Hardy",
    "gender": "female",
    "phone": "+1 (944) 557-2486"
  },
  {
    "age": 34,
    "name": "Monique Carey",
    "gender": "female",
    "phone": "+1 (863) 424-2359"
  },
  {
    "age": 20,
    "name": "Marianne Britt",
    "gender": "female",
    "phone": "+1 (846) 462-2844"
  },
  {
    "age": 37,
    "name": "Guy Langley",
    "gender": "male",
    "phone": "+1 (905) 401-3848"
  },
  {
    "age": 40,
    "name": "Hurst Hogan",
    "gender": "male",
    "phone": "+1 (934) 587-3143"
  }
]
```

The expected output should be like this.

[
  "+1 (850) 469-2827",
  "+1 (825) 558-2599",
  "+1 (946) 495-3285",
  "+1 (948) 406-2941",
  "+1 (903) 413-2132"
]

Save the query to filename answer11.sh under root directory.


**answer11.sh**

```sh
cat input.json | jpath '$[0:5].phone'
[
  "+1 (850) 469-2827",
  "+1 (825) 558-2599",
  "+1 (946) 495-3285",
  "+1 (948) 406-2941",
  "+1 (903) 413-2132"
]
```

12. Develop a JSON path query to extract the age of last 5 users
A file named input.json is provided in the terminal. Provide the file as input by the cat command.
for example, the command should be like this cat filename | jpath $query

The expected output should be like this.

[
  24,
  34,
  20,
  37,
  40
]

Save the query to filename answer12.sh under root directory.


**answer12.sh**

```sh
cat input.json | jpath '$[-5:].age'
[
  24,
  34,
  20,
  37,
  40
]
```

















