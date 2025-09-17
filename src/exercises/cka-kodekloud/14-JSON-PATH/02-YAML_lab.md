# YAML Lab

A winner is a dreamer who never gives up.

– Nelson Mandela

1. Which of the following is used to separate the key and value in YAML?

**colon**

2. How many array keys are there in the following yaml snippet?

Fruits:
  - Orange
  - Apple
  - Banana
Vegetables:
  - Carrot
  - CauliFlower
  - Tomato

Answer: **there are two arrays**

3. Which of the following statements is true?

Diccionario un una lista desordenada y list es ordenada

4. There is a yaml file named practice.yaml under ```/home/bob/playbooks/``` directory with a key property1 and value value1.

Add an additional key named property2 and value value2.

```yaml
property1: value1
property2: value2
```

5. We have updated the /home/bob/playbooks/practice.yaml file with the key name and value apple. Add some additional properties (given below) to the dictionary.

name= apple
color= red
weight= 90g

```yaml
name: apple
color: red
weight: 90g
```

6. We have updated the /home/bob/playbooks/practice.yaml file with a dictionary named employee. Add the remaining properties to it using information from the table below.

```yaml
employee:
  name: john
  gender: male
  age: 24
```

7. Now, update the /home/bob/playbooks/practice.yaml file with a dictionary in dictionary.

Add a dictionary named address to add the address information in this file.

```yaml
employee:
  name: john
  gender: male
  age: 24
  address:
    city: edison
    state: new jersey
    country: united states
```

8. We have updated the ``` /home/bob/playbooks/practice.yaml ``` file with an array of apples. Add a new apple to the list to make it a total of 4.

```yaml
- apple
- apple
- apple
- apple
```

9. In /home/bob/playbooks/practice.yaml, add two more values apple to make it 6.

```yaml
- apple
- apple
- apple
- apple
- apple
- apple
```

10. We have updated the /home/bob/playbooks/practice.yaml file with some data for apple, orange and mango. Just like apple, we would like to add additional details for each item, such as color, weight etc. Modify the remaining items to match the below data.

```yaml
- name: apple
  color: red
  weight: 100g
- name: orange
  color: orange
  weight: 90g
- name: mango
  color: yellow
  weight: 150g
```

11. We have updated the /home/bob/playbooks/practice.yaml file with a dictionary named employee. We would like to record information about multiple employees. Convert the dictionary named employee to an array named employees.

```yaml
employees:
  - name: john
    gender: male
    age: 24
```

12. Update the /home/bob/playbooks/practice.yaml file to add an additional employee (below the existing entry) to the list using the below information.

```yaml
employees:
  - name: john
    gender: male
    age: 24
  - name: sarah
    gender: female
    age: 28
```

13. We have updated the /home/bob/playbooks/practice.yaml file to add some more details. Now add the employee payslip information by adding an array called payslips. Remember, while address is a dictionary, payslips is an array of month and amount.

```yaml
employee:
  name: john
  gender: male
  age: 24
  address:
    city: edison
    state: new jersey
    country: united states
  payslips:
    - month: june
      amount: 1400
    - month: july
      amount: 2400
    - month: august
      amount: 3400
```