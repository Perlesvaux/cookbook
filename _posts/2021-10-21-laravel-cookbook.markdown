---
layout: post
title:  "Laravel (PHP)"
date:   2023-10-21 09:10:33 -0600
categories: java
tags: laravel php mvc 
---
* Table of Contents
{:toc}

## Install php & php-xml
### Option 1
```bash
sudo apt update
sudo apt install php
sudo apt install php-xml
php -v  #Check version with
php -ini | grep php.ini  #Check config file with
/etc/php/8.1/cli/php.ini  #Config file location
```

```bash
#Run server: go to project's directory and do:
php -S localhost:8000
```

### Option 2
Install [XAMPP](https://www.apachefriends.org/es/download.html)
```bash
/opt/lampp/etc/php.ini  #Config file location
#Run server:
sudo /opt/lampp/lampp start
#turn it off:
sudo /opt/lampp/lampp stop
```

### Setup config file php.ini
```bash
#Un-comment following extensions:
extension=curl
extension=fileinfo
extension=gd
extension=mysqli
extension=openssl

#Enable those PDO you'll be using.
extension=pdo_firebird
extension=pdo_mysql
extension=pdo_oci
extension=pdo_odbc
extension=pdo_pgsql
extension=pdo_sqlite
```

## 1- Installing Node and npm through nvm
Install nvm (Node Version Manager)
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
nvm install 18
nvm use 18
```

## 2- install composer (package manager) in current directory
Go [here](https://getcomposer.org/download/)  
...and follow CLI instructions.

You should now have a "composer.phar" on current directory.

```bash
#Add it to ~/bin/ to make it accessible from anywhere
mv composer.phar ~/bin/composer
```

## 3- create Laravel project
```bash
php composer.phar create-project laravel/laravel example-app
```
or, if you did "mv composer.phar ~/bin/composer", then:
```bash
composer create-project laravel/laravel example-app
```
After project has been created, start Laravel's local development server:
```bash

cd example-app
php artisan serve  #or ./artisan serve

```

## 4- This example uses MySQL 
For most use-cases, an empty database & a single user with enough privileges will be enough
```bash
sudo apt install mysql-server
sudo mysql_secure_installation
systemctl status mysql
sudo mysql
```

```sql
CREATE DATABASE databasename;
CREATE USER 'username'@'localhost' IDENTIFIED BY 'enterPasswordHere';
GRANT CREATE, DROP, ALTER, INSERT, SELECT, UPDATE, DELETE ON databasename.* to 'username'@'localhost';
```
New user can now access MySQL like this:

```bash
mysql -u username -p
```
```sql
USE DATABASE databasename;
```

Or, with:
```bash
mysql -h localhost -u username -p databasename
```

## 5- Configure your project's database settings:
Edit your example-app/.env file accordingly.
```bash
#configure database:
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=databasename
DB_USERNAME=username
DB_PASSWORD=PaSSword*
```

## 6- Let's setup the model, migration and controller:

```bash
./artisan make:model professor -mc
```

This will automatically create:

--->**Model [app/Models/professor.php]**
Each model class defines a mapping between the database table and the application code.
This mapping is based on naming conventions.
i.e.: 'professor' model maps to a 'professors' database table.

--->**Migration [database/migrations/2023_10_19_094147_create_professors_table.php]**
Migrations help you create new tables in your database or change existing ones.
Each migration file contains instructions on what the table should look like.
Like a database 'blueprint' or 'cookbook'.

--->**Controller [app/Http/Controllers/ProfessorController.php]**
Controllers organize your application's logic into reusable and maintainable classes.
i.e.: rendering views or returning JSON data.

## 7- Migration:
Go to the **[database/migrations/2023_10_19_094147_create_professors_table.php]**
Edit the "up()" function to include the columns you want:

```php
public function up(): void
{
Schema::create('professors', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->integer('age');
    $table->timestamps(); /*if you don't want this column, pay attention to next step!*/
});
}
```

If database doesn't yet contain 'professors' table, this command below will create it:
```bash
./artisan migrate

#or this one below to reset whole table and start over:
./artisan migrate:fresh
```
## 8- Model 
Go to the **[app/Models/professor.php]**
Right below where it says "use HasFactory", add:
```php
class professor extends Model
{   
    use HasFactory; 

    public $timestamps = false; /*delete this line if you're okay including timestamps!*/

    protected $table = 'professors';

    protected $fillable =[
     'name'.
     'age'
    ];
}
```

## 9- Controller:
Go to the **[app/Http/Controllers/ProfessorController.php]**
```php
use App\Models\professor; #Import professor model!

class ProfessorController extends Controller
{
    /* RETRIEVE
    | Query database, look for all professors.
    | Assign all entries to a list $teachers.
    | Pass this data from controller to view
    | through template variable 'roster'.
    | View rendered: 'resources/views/allOfThem.blade.php'.
    */
    public function retrieve(){
        $teachers = professor::all();
        return view('allOfThem', ['roster' => $teachers]);
    }

    /* CREATE
    | Render form: 'resources/views/newProf.blade.php'
    |
    | $request receives form-data as JSON.
    | Map the value of each key to a property
    | of a professor instance.
    | Pass this object from controller to view
    | through template variable 'prof'.
    | Once database is updated, this view is rendered:
    | 'resources/views/formSubmitted.blade.php'.
    */
    public function start_create(){
         return view('newProf');
     }

    public function commit_create(Request $request){
        $entry = new professor;
        $entry->name = $request->input('name');
        $entry->age  = $request->input('age');
        $entry->save();
        return view('formSubmitted', ['prof'=>$entry]);
    }

    /* UPDATE
    | Locate the professor that matches the $identifier. 
    | Pass this data from controller to view through
    | template variable 'old'. Rendered view will be this
    | form: 'resources/views/updateProf.blade.php' 
    |
    | $request receives form-data as JSON.
    | Map the value of each key to a property of an
    | already existing database entry. Pass that object from
    | controller to view through template variable 'prof'.
    | Render view: 'resources/views/formSubmitted.blade.php'
    */
    public function start_update($identifier){
        $entry = professor::find($identifier);
        return view('updateProf', ['old' => $entry]);
    }

    public function commit_update(Request $request, $identifier){
        $entry = professor::find($identifier);
        $entry->name = $request->input('name');
        $entry->age  = $request->input('age');
        $entry->save();
        return view('formSubmitted', ['prof' => $entry]);
    }

    /* DELETE
    | Locate the professor that matches the $identifier. 
    | Pass this data from controller to view through
    | template variable 'old'. Rendered view will be this
    | form: 'resources/views/deleteProf.blade.php' 
    | 
    | Upon form-submission, selected professor is
    | deleted from database. View rendered is:
    | 'resources/views/allOfThem.blade.php'
    | and its template variable 'roster' is mapped
    | to a list of all database entries.
    */
    public function start_delete($identifier){
        $entry = professor::find($identifier);
        return view('deleteProf', ['old' => $entry]);
    }

    public function commit_delete($identifier){
        $entry = professor::find($identifier);
        $entry->delete();
        return view('allOfThem', ['roster'=>professor::all()]);
    }
}
```

## 10- Web Routes:
Web routes are typically used for handling views.
We'll now map routes to controller functions.

Go to **routes/web.php**

```php
use App\Http\Controllers\ProfessorController; #Import controller!

#GET request. Endpoint 'home' maps method 'retrieve' to URL '/homepage'.
Route::get('/homepage',  [ProfessorController::class, 'retrieve'])->name('home');


#GET request (renders a form). Endpoint 'add' maps method 'start_create' to URL '/newOne' .
Route::get('/newOne', [ProfessorController::class, 'start_create'])->name('add');

#POST request. Endpoint 'adding' maps method 'commit_create' to URL '/newEntry'. 
Route::post('/newEntry', [ProfessorController::class, 'commit_create'])->name('adding');


/*GET request (renders a form). Endpoint 'update' maps method 'start_update'
  to URL '/update_item/' with route parameter 'id'*/
Route::get('/update_item/{id}',   [ProfessorController::class, 'start_update'])->name('update');

/*PUT request. Endpoint 'updating' maps method 'commit_update'
  to URL '/update_submit/' with route parameter 'id'*/
Route::put('/update_submit/{id}', [ProfessorController::class, 'commit_update'])->name('updating');


/*GET request (renders a form). Endpoint 'delete' maps method 'start_delete'
  to URL '/delete_item/' with route parameter 'id'*/
Route::get('/delete_item/{id}',     [ProfessorController::class, 'start_delete']);

/*DELETE request. Endpoint 'deleting' maps method 'commit_delete'
  to URL '/delete_submit/' with route parameter 'id'*/
Route::delete('/delete_submit/{id}', [ProfessorController::class, 'commit_delete'])->name('deleting');

```

## 11- Views:
```bash
#Create this directory:
mkdir public/css/

#Create and edit your css file:
touch public/css/app.css
```
Make 'layouts' to recycle style across child-templates:

```bash
mkdir resources/views/layouts/
touch resources/views/layouts/parent.blade.php
```
Yielded sections are placeholders for child-templates to fill-in.
```html 

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@yield('pagename')</title>

    <!--Pay attention! This is how you link the css file!-->
    <link href="{{ asset('css/app.css') }}" rel="stylesheet">

  </head>
<body>
@yield('contents')
</body>
</html>

```
Go to **resources/views/** and create these blade templates:

--->**[resources/views/allOfThem.blade.php]**
```php
@extends('layouts.parent')

@section('pagename')
Homepage
@endsection

@section('contents')
<!--Pay attention! see how route-parameters are passed to endpoints! -->
<table>
@foreach($roster as $member)
    <tr>
        <td>{{$member->name}}</td>
        <td>{{$member->age}}</td>
        <td><a href="{{route('update', ['id'=>$member->id])}}">edit</a></td>
        <td><a href="{{route('delete', ['id'=>$member->id])}}">delete</a></td>
    </tr>
@endforeach
</table>
<a href="{{route('add')}}">Add new professor!</a>
@endsection
```

--->**[resources/views/newProf.blade.php]**
```php
@extends('layouts.parent')
@section('pagename')
...Adding
@endsection
@section('contents')
<form method="POST" action="{{ route('adding') }}">
    @csrf <!-- Cross-Site Request Forgery token -->
    Known as: <input type="text" name="name" required> <br>
    Winters: <input type="number" name="age" required> <br>
    <button type="submit">Add new entry</button>
</form>
<a href="{{route('home')}}">Cancel</a>
@endsection
```
--->**[resources/views/updateProf.blade.php]**
```php
@extends('layouts.parent')
@section('pagename')
...Updating
@endsection
@section('contents')
<form method="POST" action="{{ route('updating', ['id' => $old->id]) }}">
    @csrf <!-- Cross-Site Request Forgery token -->
    @method('PUT') <!-- Specify that it's a PUT request -->
    A.K.A: <input type="text" name="name" value="{{$old->name}}" required> <br>
    Summers: <input type="number" name="age" value="{{$old->age}}" required> <br>
    <button type="submit">Submit changes</button>
</form>
<a href="{{route('home')}}">Cancel</a>
@endsection
```
--->**[resources/views/deleteProf.blade.php]**
```php
@extends('layouts.parent')
@section('pagename')
...Deleting
@endsection
@section('contents')
Do you wanna permanently delete "{{$old->name}}"?
<form method="POST" action="{{ route('deleting', ['id' => $old->id]) }}">
    @csrf <!-- CSRF token -->
    @method('DELETE') <!-- Specify that it's a DELETE request -->
    <button type="submit">Yes</button>
</form>
<a href="{{route('home')}}">Cancel</a>
@endsection
```
--->**[resources/views/formSubmitted.blade.php]**
```php
@extends('layouts.parent')
@section('pagename')
Success!
@endsection
@section('contents')
{{$prof->name}} (age: {{$prof->age}}) added<br>
<a href="{{route('home')}}">back to homepage</a>
@endsection
```

## 12- Miscellaneous:
### Common Paths

```bash
example-app/public/css            #i.e.: CSS files
example-app/resources/views/      #i.e.: Blade templates and layouts
example-app/app/Models            #i.e.: Models
example-app/app/Http/Controllers/ #i.e.: ModelControllers
example-app/routes/web.php        #i.e.: MVC controller
```

### Authentication scaffolding
**Be careful!** This will overwrite your **routes/web.php** file.
'Breeze' package protects your endpoits with user-authentication:
```bash
composer require laravel/breeze --dev
./artisan breeze:install  #Blade with Alpine/ Darkmode/ PHPUnit
./artisan migrate
npm run dev   #Activates authentication service. Run on different terminal
```
Add your endpoints under middleware('auth')->group(function(){...}):

```php
use App\Http\Controllers\ProfessorController; #Import controller!
#{...}
Route::middleware('auth')->group(function () {
    #These were auto-generated
    Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');
    Route::patch('/profile', [ProfileController::class, 'update'])->name('profile.update');
    Route::delete('/profile', [ProfileController::class, 'destroy'])->name('profile.destroy');

    #Here you can put the endpoints previously defined on step 10.
    #Important: Any endpoint left out of this scope remains accessible without authentication.
});
```

### Generating PDF reports

```bash
composer require barryvdh/laravel-dompdf
```

Edit config/app.php
Look for this comment:

```php
    /*
     * Application Service Providers...
     */
```

Add this one to the list of 'providers':
```php
Barryvdh\DomPDF\ServiceProvider::class,
```
Time to update the **app/Http/Controllers/ProfessorController.php**:
```php
use PDF #Dont forget this import!

public function getPDF(){
    $teachers=professor::all();
    $pdf = PDF::loadView('allOfThem', ['roster'=>$teachers]);
    return $pdf->stream('rosterInPDF.pdf');
    #return $pdf->download('downloadedRoster.pdf'); 
}
```
Finally, map it to a route at: **routes/web.php**

```php
/*GET request (renders a view as a PDF). Endpoint 'pdf'
 Maps method 'getPDF' to URL '/generatePDF'.*/
Route::get('/generatePDF', [ProfessorController::class, 'getPDF'])->name('pdf');
```

Word of caution!. PDF rendering can be very slow if 
CSS used in the view is from: **public/css/**

```html
i.e.: this would take a full minute to render!
<link href="{{ asset('css/app.css') }}" rel="stylesheet">

Therefore, a <style> tag or in-line CSS is preferred!
```
