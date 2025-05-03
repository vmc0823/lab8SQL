The Schema class
●	We want to code a Tuple class that can hold rows of any number of columns and any datatype.  To do this we use a Schema class that contains the column names and datatypes for a table row.  As a simplification we will only allow only integer and a varchar type and only a single column primary key.   To create a schema with ID primary key of integer type, name and dept_name as varchar types, and salary as integer type
     Schema s = new Schema();
     s.addKeyIntType("ID");
     s.addVarCharType("name", 25);
     s.addVarCharType("dept_name", 25);
     s.addIntType("salary");
●	Study the schema class carefully and see that there are methods to return the number of columns, the column index given a column name, and given a column index return the column name, type and maximum size.  After studying the schema class you should be able to answer the following questions.
1.	Which Schema method returns the column name of the 3rd column?
2.	Which method returns the primary key column name?
3.	Which method returns the column index of the column with name “salary”?
4.	What is the code to determine the data type of column “dept_name”?
The Tuple class
●	The Tuple contains the data values for a row according to the schema specification.  To create a tuple for an instructor the code would be
   Tuple t = new Tuple (s, 19803, “Wisneski”, “Comp. Sci.”, 49000);
where the first argument “s” is the schema and the remaining arguments are the column values.   
Study the methods of Tuple class and  be able to answer the following questions. 
1.	Which method returns the integer value of the 3rd column?
2.	Which method returns the value of the “salary” column?
3.	What code returns the primary key value of the tuple?
4.	What code sets the value of the “salary” column to 72000?
●	Complete the TODOs in Table class and complete the insert, delete and lookup methods. 
When adding a tuple into the arraylist, it is important to use the copy constructor of the Tuple class and to add a copy of the tuple, and not the tuple itself.
	tuples.add(new Tuple(rec)); 
Why is this important?  Consider the case of a program that inserts a tuple to a table, but then modifies the tuple after the insert.  Such as 
Schema s = new Schema();
s.addKeyInt(“key”);
s.addInt(“cola”);
Table table = new Table(s);
Tuple tuple = new Tuple(s, 10, 20);
table.insert(tuple);
tuple.set(0, 30);
tuple.set(1, 40);
table.insert(tuple);

How many tuples are in the table and what are their values? The answer is the arraylist contains one tuple with values (30, 40).   The first insert added a reference to a tuple with values (10, 20).  But then the tuple value was changed to (30, 40) and the second insert returned false because the key value 30 is already in the arraylist.

When returning a tuple from the lookup method, return a copy of the tuple, and not a reference to the tuple itself.  Consider this case where a program does a lookup and then changes the primary key of the tuple to a duplicate value

	Table table = new Table(s);
	Tuple tuple1 = new Tuple(s, 10, 20);
	Tuple tuple2 = new Tuple(s, 30, 40);
	table.insert(tuple1);
	table.insert(tuple2);
	Tuple tuple3 = table.lookup(10);
	tuple3.set(0, 30);

At this point, there are 2 tuples in tables.  What are their values?  The first is (30, 20) and second is (30, 40).  This violates the primary key constraint of the table.

So return a copy of the tuple using the copy constructor and not a reference to the tuple in the arraylist.

●	Execute the unit tests in the TableTest class.
●	Code a main application Java class that demonstrates using Schema and Tuple classes.  The main method should create a Table object with the schema for instructor data with columns
ID  int 
name varchar(25)
dept_name varchar(25)
salary int
ID is the primary key
o	insert 5 rows into the Table and print the table showing the 5 rows.  
o	You can print the table using the code System.out.println(table.toString());
o	delete one of the 5 instructors and print the table again to show the 4 remaining rows.
o	attempt to delete the same row and show that the delete method returns false.
o	lookup both an  row using key column ID that exists in the table and an ID that is not in the table.  Print the results.
o	demonstrate using the lookup method to search on a column that returns multiple rows, a single row and no rows. Print the result table for each lookup.
o	When I run your application class it should print results like these
[12121, Kim, Elect. Engr., 65000]
[19803, Wisneski, Comp. Sci., 46000]
[24734, Bruns, Comp. Sci., 70000]
[55552, Scott, Math, 80000]
[12321, Tao, Comp. Sci., 95000]

delete 12121:true

[19803, Wisneski, Comp. Sci., 46000]
[24734, Bruns, Comp. Sci., 70000]
[55552, Scott, Math, 80000]
[12321, Tao, Comp. Sci., 95000]

delete 12121:false

lookup 19803: [19803, Wisneski, Comp. Sci., 46000]

lookup 12121: null

eval dept_name=Comp. Sci.
[19803, Wisneski, Comp. Sci., 46000]
[24734, Bruns, Comp. Sci., 70000]
[12321, Tao, Comp. Sci., 95000]

eval ID=19803
[19803, Wisneski, Comp. Sci., 46000]

eval ID=19802
Empty Table

JAVA predicate evaluation:
The SelectQuery class has a Boolean condition which consists of And, Or equal predicates.  For simplification we will only allow equal comparisons. But after doing this assignment you should be able to see how other predicates such as greater than, less than, not equals, LIKE, IN could be done.  The way in which compound predicates using AND or OR are represented is using a tree data structure.   
An equal predicate has a column name and a value and returns true if the column value of a tuple equals the given value.  
AND and OR conditions have a left and a right condition that can be either another AND, OR, or equal condition.  
The where clause “dept_name = ‘Comp. Sci’ or (salary = 65000 and ID=12121)” would be represented as the data structure
![image](https://github.com/user-attachments/assets/0745743d-becc-40ae-b68d-857fc06ce3db)
Calling eval method on the root node, in turns call eval on their child nodes, which in turn call eval on their child nodes.  The EQ leaf nodes compare the tuple column value and return either true or false which is propagated up the tree and combined at the AND OR nodes for the overall result of either true or false.

