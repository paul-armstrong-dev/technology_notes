# rtl-py-coding-standards
Centralized location for all RTL-python coding standards. 

### Values
- **"Build tools for others that you want to be built for you." - Kenneth Reitz**
- **"Simplicity is alway better than functionality." - Pieter Hintjens**
- **Use the case that handles every case.** (summary of google docs) 
- **Single Object Responsibility**

### General Development Guidelines

- "Explicit is better than implicit" - [PEP 20]
- "Readability counts." - [PEP 20]
- **Fix each [broken window](http://www.artima.com/intv/fixit2.html) (bad design, wrong decision, or poor code) *as soon as it is discovered*.**
- Even more important that Test-Driven Development--*Human-Driven Development*
- "Now is better than never." - [PEP 20]
- Test ruthlessly. Write docs for new features.
- These guidelines may--and probably will--change.


## In Particular

### Style

- Follow [PEP 8], when sensible.
- Use f-strings for format if on python3.6> from - [pep498](<https://www.python.org/dev/peps/pep-0498/>)
- Max line length of 100 
- Double quotes for ALL strings (handles every case) from - [GoogleStyle](<https://google.github.io/styleguide/pyguide.html>)
- Follow single object responsibility as strictly as possible, 
- Encapsulate operations into well named functions.
- Follow sonarcube max logical complexity limit per function (no functions with more than 15 operations)   

#### Naming

- Variables, functions, methods, packages, modules
    - `lower_case_with_underscores`
- Classes and Exceptions
    - `CapWords`
- Protected methods and internal functions
    - `_single_leading_underscore(self, ...)`
- Private methods
    - `__double_leading_underscore(self, ...)`
- Constants
    - `ALL_CAPS_WITH_UNDERSCORES`

##### General Naming Guidelines 

Avoid one-letter variables (esp. `l`, `O`, `I`). 


Ideally name functions as verbosely as possible 
According to their function ie: 

	 def main(self):
        """
        Main function for the daily process
        :return:
        """
        df = self.prepare_data(self.open_file())
        self.save_file(df=df, location="local")
        self.delete_from_sql()
        self.write_to_sql(dataframe=df)
        self.archive_file()
        self.create_view()
        exit(0)
----


***Exceptions***: In very short blocks, when the meaning is clearly visible from the immediate context
Ideally do not use excepts where possible, try actually handle the error through testing and correcting code.

**Never except Exception**

--------------
**Avoid redundant labeling.**
 
**Yes**
```python
import audio

core = audio.Core()
controller = audio.Controller()
```

**No**
```python
import audio

core = audio.AudioCore()
controller = audio.AudioController()
```

--------------
**Prefer "reverse notation".**

**Yes**
```python
elements = ...
elements_active = ...
elements_defunct = ...
```

**No**
```python
elements = ...
active_elements = ...
defunct_elements ...
```

--------------
**Avoid getter and setter methods.**

**Yes**
```python
person.age = 42
```

**No**
```python
person.set_age(42)
```

#### Indentation

Use 4 spaces--never tabs. Enough said.


##### Line Length
- 80 seems too strict so can compromise at 100
- Ideally configure IDE to warn on this

#### Imports

Import entire modules instead of individual symbols within a module. For example, for a top-level module `canteen` that has a file `canteen/sessions.py`,

**Yes**

```python
import canteen
import canteen.sessions
from canteen import sessions
```

**No**

```python
from canteen import get_user  # Symbol from canteen/__init__.py
from canteen.sessions import get_session  # Symbol from canteen/sessions.py
```

*Exception*: For third-party code where documentation explicitly says to import individual symbols.

*Rationale*: Avoids circular imports. See [here](https://sites.google.com/a/khanacademy.org/forge/for-developers/styleguide/python#TOC-Imports).

Put all imports at the top of the page with three sections, each separated by a blank line, in this order:

1. System imports
2. Third-party imports
3. Local source tree imports

*Rationale*: Makes it clear where each module is coming from.

<https://www.python.org/dev/peps/pep-0008/#imports>

--

#### Rtl Specfics 
##### Environment variables
Would like to (likely more feasible once we have vault)
  
	Standardise environment variables(along with the names) (eg: 
	       sql_user = os.environ['MSSQL_USER']
	        sql_password = os.environ['MSSQL_PASSWORD']
	        sql_server = os.environ['MSSQL_SERVER_IP']
	        sql_port = os.environ['MSSQL_PORT']
	        sql_db = os.environ['MSSQL_DB']
--
	Similary, if possible, would like to standardize date variables and how they are handled, 
		- Likely the easiest option for receiving functions is to have start_date and end_date
		any time we use a date. So run(start_date="", end_date="") 
		- But would also like to see with regards to days back etc, 
		I know this is used a lot on SQL especially and would like them to be handled in the same 
		Way, so days_back = start_date end_date, but not startdate = days_back(-day) 

<https://dateutil.readthedocs.io/en/stable/relativedelta.html>

##### logging
- <https://github.com/Delgan/loguru>

##### templating
- <https://github.com/cookiecutter/cookiecutter>
- Provide some simple examples on the above
- Class template

##### pycharm settings repo? 
- To be discusses

##### requirements

Needs to be in the repo, ***and versioned***.

Steps to create correctly, everytime.

1. create venv for repo as start of work
2. Do pip > requirements.txt before checking in repo
3. Checkin requirements.txt

--	

### Notes on performance:
###### List Comprehension
More of a note on performance than on style guides
List comprehensions should only be used when actually storing the output of the list comprehension, 
If you are modifying a list or dict rather use the traditional syntax:
    `for x in y:
          x=x+1`
          
-- 

###### Numpy 
There’s a couple of points we can follow when looking to speed things up:
- If there’s a for-loop over an array, there’s a good chance 
  we can replace it with some built-in Numpy function
- If we see any type of math, there’s a good chance 
  we can replace it with some built-in Numpy function

<https://towardsdatascience.com/one-simple-trick-for-speeding-up-your-python-code-with-numpy-1afc846db418>
<https://www.datacamp.com/courses/writing-efficient-python-code>

--

##### .env files
Always close values in "", can handle every variable type

--


Useful docs & further reading:
--

- <https://github.com/joshnh/Git-Commands>
- <https://12factor.net/>

Sources 
--

- <https://www.python.org/dev/peps/pep-0008/#imports>
- <https://www.python.org/dev/peps/pep-0498/>
- <https://google.github.io/styleguide/pyguide.html>
- <http://www.moderndescartes.com/essays/python_list_comprehensions/index.html>
- <https://medium.com/@collectiveacuity/argparse-vs-click-227f53f023dc>
- <https://github.com/reddit-archive/reddit/wiki/PythonImportGuidelines>
- <https://code.google.com/archive/p/soc/wikis/PythonStyleGuide.wiki>
- <https://docs.openstack.org/hacking/latest/>
- <https://www.python.org/dev/peps/pep-0008/> 
