Step 2: Install maatwebsite/excel Package

In this step we need to install maatwebsite/excel package via the Composer package manager, so one your terminal and fire bellow command:

composer require maatwebsite/excel

Now open config/app.php file and add service provider and aliase.

config/app.php

'providers' => [

	....

	Maatwebsite\Excel\ExcelServiceProvider::class,

],

'aliases' => [

	....

	'Excel' => Maatwebsite\Excel\Facades\Excel::class,

],

Read Also: Laravel 8 CRUD Application Tutorial for Beginners

Step 3: Create Dummy Records

In this step, we have to require "users" table with some dummy records, so we can simply import and export. So first you have to run default migration that provided by laravel using following command:

php artisan migrate

After that we need to run following command to generate dummy users:

php artisan tinker

User::factory()->count(20)->create()

Step 4: Add Routes

In this step, we need to create route of import export file. so open your "routes/web.php" file and add following route.

routes/web.php

<?php

  

use Illuminate\Support\Facades\Route;

    

use App\Http\Controllers\MyController;

  

/*

|--------------------------------------------------------------------------

| Web Routes

|--------------------------------------------------------------------------

|

| Here is where you can register web routes for your application. These

| routes are loaded by the RouteServiceProvider within a group which

| contains the "web" middleware group. Now create something great!

|

*/

  

Route::get('importExportView', [MyController::class, 'importExportView']);

Route::get('export', [MyController::class, 'export'])->name('export');

Route::post('import', [MyController::class, 'import'])->name('import');

Step 5: Create Import Class

In maatwebsite 3 version provide way to built import class and we have to use in controller. So it would be great way to create new Import class. So you have to run following command and change following code on that file:

php artisan make:import UsersImport --model=User

app/Imports/UsersImport.php

<?php

  

namespace App\Imports;

  

use App\Models\User;

use Maatwebsite\Excel\Concerns\ToModel;

use Maatwebsite\Excel\Concerns\WithHeadingRow;

  

class UsersImport implements ToModel, WithHeadingRow

{

    /**

    * @param array $row

    *

    * @return \Illuminate\Database\Eloquent\Model|null

    */

    public function model(array $row)

    {

        return new User([

            'name'     => $row['name'],

            'email'    => $row['email'], 

            'password' => \Hash::make($row['password']),

        ]);

    }

}

You can download demo csv file from here: Demo CSV File.

Step 6: Create Export Class

maatwebsite 3 version provide way to built export class and we have to use in controller. So it would be great way to create new Export class. So you have to run following command and change following code on that file:

php artisan make:export UsersExport --model=User

app/Exports/UsersExport.php

<?php

  

namespace App\Exports;

  

use App\Models\User;

use Maatwebsite\Excel\Concerns\FromCollection;

  

class UsersExport implements FromCollection

{

    /**

    * @return \Illuminate\Support\Collection

    */

    public function collection()

    {

        return User::all();

    }

}

Step 7: Create Controller

In this step, now we should create new controller as MyController in this path "app/Http/Controllers/MyController.php". this controller will manage all importExportView, export and import request and return response, so put bellow content in controller file:

app/Http/Controllers/MyController.php

<?php

     

namespace App\Http\Controllers;

    

use Illuminate\Http\Request;

use App\Exports\UsersExport;

use App\Imports\UsersImport;

use Maatwebsite\Excel\Facades\Excel;

    

class MyController extends Controller

{

    /**

    * @return \Illuminate\Support\Collection

    */

    public function importExportView()

    {

       return view('import');

    }

     

    /**

    * @return \Illuminate\Support\Collection

    */

    public function export() 

    {

        return Excel::download(new UsersExport, 'users.xlsx');

    }

     

    /**

    * @return \Illuminate\Support\Collection

    */

    public function import() 

    {

        Excel::import(new UsersImport,request()->file('file'));

             

        return back();

    }

}

Step 8: Create Blade File

In Last step, let's create import.blade.php(resources/views/import.blade.php) for layout and we will write design code here and put following code:

resources/views/import.blade.php

<!DOCTYPE html>

<html>

<head>

    <title>Laravel 8 Import Export Excel to database Example - ItSolutionStuff.com</title>

    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.1.3/css/bootstrap.min.css" />

</head>

<body>

   

<div class="container">

    <div class="card bg-light mt-3">

        <div class="card-header">

            Laravel 8 Import Export Excel to database Example - ItSolutionStuff.com

        </div>

        <div class="card-body">

            <form action="{{ route('import') }}" method="POST" enctype="multipart/form-data">

                @csrf

                <input type="file" name="file" class="form-control">

                <br>

                <button class="btn btn-success">Import User Data</button>

                <a class="btn btn-warning" href="{{ route('export') }}">Export User Data</a>

            </form>

        </div>

    </div>

</div>

   

</body>

</html>

Now you can check on your laravel 8 application.

Now we are ready to run our example so run bellow command so quick run:

php artisan serve
