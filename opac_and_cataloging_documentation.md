#Basic OPAC and Cataloging Module
##Documentation Portfolio

**Introduction**
Every library must provide information about its collection to its patrons. Generally, the primary point of access to a 
library's holdings is the **O**nline **P**ublic **A**ccess **C**atalog (OPAC). The OPAC contains bibliographic information
about items in the library which will assist patrons in locating desired resources; this information includes metadata such
as title, author, publication information, call number/location, and subject headings/keywords as well as patron reviews 
and recommendations of similar items (on occasion).

The OPAC is the first point of search for catalog items, but it is not a standalone system. The OPAC is the visual portion
of a much wider system composed of websites and relational databases. The databases contain the bibliographic records 
storing the information about the library's items, and the websites share those records and make them searchable to the
public (hence "online public access catalog").

**Relational Database Structure**
Relational databases, such as those created by `MySQL`, store information in datatables. These tables can then be searched 
in a number of ways to recall the desired records. Relational databases in particular allow users to search and recall
information stored in multiple datatables simultaneously, yielding composite/related search results. For the bibilographic 
records contained in an OPAC, relational databases may have tables featuring the relevant metadata (title, author, etc.) 
as well as the "hidden" information found in MARC21-format catalog records (e.g., ISBN, subject headings, DDC/LCC number, 
etc.). 

When a usesr conducts a search in an OPAC, the search fields correspond to field IDs in datatables in the database. The
field IDs operate as search parameters to guide the search to the proper columns/rows in the table. The search engine 
results page (SERP) then displays the records which match the search parameters.

**Step-by-Step Setup**
*Connecting to the Database*
A LAMP stack can use `Apache 2`, `MySQL`, and `PHP` to create a barebones OPAC. First, a database of books/items must be
created in `MySQL`. After this, a webpage for the OPAC will be created using `HTML` and stored on an `Apache 2` server.
Finally, a `PHP` script can be inserted into/reference by the `HTML` OPAC search page to provide a connection to the
`MySQL` database. 

After creating credentials for your OPAC database in `MySQL`, you need to create an authentication script with `PHP`; see
previous documentation on establishing a LAMP stack. When all is ready to go, your OPAC will need to use those credentials
to access the database; this is done with the following `PHP` script in your `HTML` OPAC page:
```
require_once '/var/www/login.php`;
$conn = new mysqli($db_hostname, $db_username, $db_password, $db_database);
```
(Don't forget to always bound your `PHP` with the appropriate `<?php` and `?>`

Other `PHP` added to your OPAC (a `searh.php` file linked as a form to your `HTML` OPAC page) will define search parameters 
for the database fields you wish to make searchable and display results. 

*Structure of the Cataloging Module*
Like the OPAC itself, the cataloging module will need to consist of a webpage and a `PHP` script to link your page to
the database itself. The search page can be created however you wish, but it *must* contain the following to link to your 
database-accessing `PHP` script, here named `insert.php`:
```
<form action="insert.php" method="post">
```
From there, you can add search fields (described below).

The `insert.php` script itself must establish a connection to the database stored on your Linux machine and allow you to
insert items into the correct table and fields. For our barebones OPAC with only four fields, the (separate) `PHP` script 
looks like this:
```
$stmt = $conn->prepare("INSERT INTO book (author, title, publisher, copyright) VALUES (?, ?, ?, ?)");
$stmt->bind_param("ssss", $author, $title, $publisher, $copyright);
$author = $_POST["author"];
$title = $_POST["title"];
$publisher = $_POST["publisher"];
$copyright = $_POST["copyright"];
```
Other scripts will then be added to display results (success or error), report errors, and close the connection.

Those four fields are now accessible to the `HTML` side of things, which must similarly include a form with boxes sharing
those labels (author, title, publisher, and copyright) and allowing input. To do that, you will need to create this `HTML`
code once for each field to be added to the table in your OPAC database following the above `<form>` line:
```
<label for="author">Author:</label>
<input type="text" name="author" id="author" required><br><br>
```
Be sure to change the label name, input name, and id to match your table parameters. You'll also need to change the input
type to `"number"` for copyright years and establish a date range after the `id` tag with `min=XXXX max="XXXX`, with `XXXX`
being the four-digit years of your range.

One you've added any other `HTML` or `CSS` to your page, you're (almost) ready to go!

*OPAC: Search and Retrieval*
The barebones OPAC created will allow searching by keyword/terms and date range (other criteria can be added on the OPAC page
created earlier by adding `input` fields in the `form` under "My Basic Library OPAC"). When search terms are entered (date
range is required in our OPAC), the `PHP` search script connects to the OPAC database in `MySQL` and scans the datatables in
the database, matching `input` field data with information stored in the database under the corresponding table header 
(here, author, title, publisher, and copyright). The use of `echo` in the `search.php` script will then display the 
information retrieved in the order listed. If corresponding information cannot be located in the tables, code in the 
`search.php` script will return "No results found."

In short: terms entered onto the `HTML` search engine will run through a `PHP` script to match them with information stored
in the tables of the `MySQL` database and display those records in the SERP. Simple! 

*Scaling Up: Making this a Real-World OPAC/Cataloging Module*
Were this to become a real-world OPAC/cataloging module, many more fields would have to be present in the database and 
available for search in the `HTML`/`PHP` pages for the OPAC. MARC21 records are complex, and each and every type of metadata
listed in them would have be input into a table/database and made accessible in the code for the OPAC. The cataloging 
module created here would therefore be expanded as well to accommodate the new fields to be written to the table. It would
also help to use a bit of `CSS` to make the OPAC visually pleasing as well. In short, however, a real-world OPAC and 
cataloging module follows this basic blueprint, but it is scaled up in every conceivable way, from the table design to the
search parameters in the code itself.

*Additional Configuration Considerations*
The `MySQL` database should be properly configured before beginning work on the OPAC and cataloging module (see previous 
documentation).

It will be necessary, however, to modify `Apache 2` files in order to permit your pages to access the database. Since
ideally all web connections can only access specific information on the server, you will need to set those boundaries
and make sure the database and *only* the database is accessible online. To do that, `cd` to the `etc/apache2` directory
and create a new authentication file:
```
sudo htpasswd -c /etc/apache2/.htpasswd username
```
while specifying a username of your choice.

You will then need to modify the `apache2.conf` file directly to permit overrides:
```
sudo nano /etc/apache2/apache2.conf
```
In nano, use `^_` to find line 172 and change `AllowOverride None` to `AllowOverride All`. Write the file and exit.

Finally, in the directory containing your catalog files (for my purposes, `/var/www/html/cataloging`), create another
file by using `sudo nano .htaccess`, adding the following:
```
AuthType Basic
AuthName "Authorization Required"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```
When opening the cataloging module, it will then require the username and password you created when you made the `.htpasswd`
file.

One final security concern: files in our `/var/www/html` directory will need to have read-only authorization so they cannot
be changed from the web. To do that, use the following commands:
```
sudo chown :www-data /var/www/html
sudo chmod -R g+s /var/www/html
```
Any new files will thus inherit the proper authorizations and permissions. 

**Key Details**
Other than the various permissions established in the configuration changes above, a few key details remain. The first is
to be careful with data types. As noted previously, not all fields to be searched are text; copyright year is a number. All
code dealing with years must therefore be treated as numbers and given acceptable ranges. This is true for the database/table
itself as well as the `HTML` and `PHP` code for the OPAC/cataloging module. Even the `bind_param` statements in the 
`search.php` script must take this into consideration (changing from `$search_param` for the other three fields to 
`$start_date` and $end_date`).

A second key detail is simple: after adding a record or conducting a search, the user should be presented with links to
return to the search page/data entry page. Do not forget to add a simple `a href=` link in your `HTML` files at the end.
Your users will thank you.

**Using Documentation**
No problems arose in using the documentation provided for this assignment. The lectures complemented the textbook well,
and I was able to follow along and get things put together without issue (other than my usual typos). I did go "off book"
to play around with relational databases a bit to make sure I understood the connections between the tables, and that 
helped cement those in my mind. The extra authentication files we created also helped me see the connections between 
the various files we made and how each was referenced by the others in order to create a working whole. Altogether, 
everything went smoothly using the provided documentation to type out the code and then to go back and practice 
searches and other commands in order to explore the created files/OPAC/cataloging module.
