# JSON PATH Wild Cards

#### Reference

[JSON Wild Cards](https://notes.kodekloud.com/docs/JSON-Path-Test-Free-Course/Lesson/JSON-PATH-2-Wild-Cards)


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

**answer1.sh**

```sh
echo "cat q1.json | jpath '$.*'" > answer1.sh
cat q1.json | jpath '$.*' >> answer1.sh 
```

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



