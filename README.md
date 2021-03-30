# shoppingCart-laravel

Shopping Cart in Laravel

Video link
https://youtu.be/9NU8-Yh2hyA
                                      
                                      How to make a ShoppingCart in Laravel
									  
									      in first subscribe me . Please

1. Make a table to Product

php artisan make:model Product -m

Schema::create('producs', function (Blueprint $table) {
            $table->increments('id');
            $table->string("name", 255)->nullable();
            $table->text("description")->nullable();
            $table->string("photo", 255)->nullable();
            $table->decimal("price", 6, 2);
            $table->timestamps();
        });
		
2. Now let’s create a Seeder to populate our products table with some data to work with. 
   Run this command to generate a seeder.

php artisan make:seed ProductsSeeder



Notice: what is seed in laravel
  Laravel includes the ability to seed your database with test data using seed classes. 
  All seed classes are stored in the database/seeders directory

3. Go to database>seeds>ProductsSeeder. Then inside run() function add this code.

DB::table('products')->insert([
           'name' => 'Samsung Galaxy S9',
           'description' => 'A brand new, sealed Lilac Purple Verizon Global Unlocked Galaxy S9 by Samsung. This is an upgrade. Clean ESN and activation ready.',
           'photo' => 'https://i.ebayimg.com/00/s/ODY0WDgwMA==/z/9S4AAOSwMZRanqb7/$_35.JPG?set_id=89040003C1',
           'price' => 698.88
        ]);

        DB::table('products')->insert([
            'name' => 'iPhone 12 Pro',
            'description' => 'GSM & CDMA FACTORY UNLOCKED! WORKS WORLDWIDE! FACTORY UNLOCKED. iPhone x 64gb. iPhone 8 64gb. iPhone 8 64gb. iPhone X with iOS 11.',
            'photo' => 'https://store.storeimages.cdn-apple.com/4982/as-images.apple.com/is/iphone-12-pro-blue-hero?wid=940&hei=1112&fmt=png-alpha&qlt=80&.v=1604021661000',
            'price' => 983.00
        ]);

        DB::table('products')->insert([
            'name' => 'iPhone 12',
            'description' => 'New condition
• No returns, but backed by eBay Money back guarantee',
            'photo' => 'https://store.storeimages.cdn-apple.com/4982/as-images.apple.com/is/iphone-12-red-select-2020?wid=470&hei=556&fmt=png-alpha&.v=1604343703000',
            'price' => 675.00
        ]);

        DB::table('products')->insert([
            'name' => 'LG V10 H900',
            'description' => 'NETWORK Technology GSM. Protection Corning Gorilla Glass 4. MISC Colors Space Black, Luxe White, Modern Beige, Ocean Blue, Opal Blue. SAR EU 0.59 W/kg (head).',
            'photo' => 'https://i.ebayimg.com/00/s/NjQxWDQyNA==/z/VDoAAOSwgk1XF2oo/$_35.JPG?set_id=89040003C1',
            'price' => 159.99
        ]);

        DB::table('products')->insert([
            'name' => 'iPhone 12',
            'description' => 'Cricket Wireless - Huawei Elate. New Sealed Huawei Elate Smartphone.',
            'photo' => 'https://www.att.com/idpassets/global/devices/phones/apple/apple-iphone-12/carousel/black/64gb/6858C-hero-black_carousel.png',
            'price' => 68.00
        ]);

        DB::table('products')->insert([
            'name' => 'HTC One M10',
            'description' => 'The device is in good cosmetic condition and will show minor scratches and/or scuff marks.',
            'photo' => 'https://i.ebayimg.com/images/g/u-kAAOSw9p9aXNyf/s-l500.jpg',
            'price' => 129.99
        ]);
		
		
4. now put the above code in database 

php artisan db:seed --class=ProductsSeeder


5. Make controller 
   php artisan make:controller ProductsController
   
   
6. put the below code in controller

use App\Product;

 public function index()  // get all data from database
    {
        $products = Product::all();

        return view('products', compact('products'));  //  show all data in page of products.blade.php
    }

    // this function is to show cart of product because we wanna show result of choose product by user in this page
    public function cart()  
    {
        return view('cart');  
    }

    

    public function addToCart($id) // by this function we add product of choose in card
    {
        $product = Product::find($id);

        if(!$product) {

            abort(404);

        }
// what is Session:
//Sessions are used to store information about the user across the requests.
// Laravel provides various drivers like file, cookie, apc, array, Memcached, Redis, and database to handle session data. 
// so cause write the below code in controller and tis code is fix
        $cart = session()->get('cart');  

        // if cart is empty then this the first product
        if(!$cart) {

            $cart = [
                    $id => [
                        "name" => $product->name,
                        "quantity" => 1,
                        "price" => $product->price,
                        "photo" => $product->photo
                    ]
            ];

            session()->put('cart', $cart);

            return redirect()->back()->with('success', 'added to cart successfully!');
        }

        // if cart not empty then check if this product exist then increment quantity
        if(isset($cart[$id])) {

            $cart[$id]['quantity']++;

            session()->put('cart', $cart); // this code put product of choose in cart

            return redirect()->back()->with('success', 'Product added to cart successfully!');

        }

        // if item not exist in cart then add to cart with quantity = 1
        $cart[$id] = [
            "name" => $product->name,
            "quantity" => 1,
            "price" => $product->price,
            "photo" => $product->photo
        ];

        session()->put('cart', $cart); // this code put product of choose in cart

        return redirect()->back()->with('success', 'Product added to cart successfully!');
    }

    // update product of choose in cart
    public function update(Request $request)
    {
        if($request->id and $request->quantity)
        {
            $cart = session()->get('cart');

            $cart[$request->id]["quantity"] = $request->quantity;

            session()->put('cart', $cart);

            session()->flash('success', 'Cart updated successfully');
        }
    }

    // delete or remove product of choose in cart
    public function remove(Request $request)
    {
        if($request->id) {

            $cart = session()->get('cart');

            if(isset($cart[$request->id])) {

                unset($cart[$request->id]);

                session()->put('cart', $cart);
            }

            session()->flash('success', 'Product removed successfully');
        }
    }



7. make layout.blade.php and add the below code


<!DOCTYPE html>
<html>
<head>

    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

    <title>@yield('title')</title>

    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">
    <link rel="stylesheet" type="text/css" href="{{ asset('css/style.css') }}">

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>

</head>
<body>

<div class="container">

    <div class="row">
        <div class="col-sm-2">
            
            @if(session('cart'))
                <a href="{{ url('cart') }}" class="btn btn-primary  mt-3 mb-3 btn-block">

                    <i class="fa fa-shopping-cart" aria-hidden="true"></i>
                     Cart
                    <!-- this code count product of choose tha user choose -->

                    <span class="badge badge-pill badge-danger">{{ count(session('cart')) }}</span>
                </a>
        </div>
                      
      
        <div class="col-sm-4 text-center">
     
                @if(session('success'))
                    <p class="btn-success  mt-3 mb-3 btn-block session" style='padding: .375rem .75rem;'>
                      {{ session('success') }}
                    </p>
        </div>

                @endif
       <!-- if user dont choose any product -->
                @else
                      
                    <a href="" class="btn text-light bg-warning mt-3 mb-3" role="button">
                    <i class="fa fa-shopping-cart" aria-hidden="true"></i>
                    Cart Empty
                    </a> 

                    @endif
                    

                    
                </div>
            </div>
        </div>
    </div>
</div>

<div class="container">
    @yield('content')
</div>


    @yield('scripts')

 <script>


$("a").click(function () {
      $(".session").visibility(2);
    });

      
    </script>

<style>
.session{
    visibility:hidden ;
    
}


body{
    background-color: #fff;
}
</style>

  </body>
</html>



8. make products.blade.php and add the below code

@extends('layout')

@section('title', 'Products')

@section('content')

    <div class="container">


        <div class="row">
 <!-- by this code show any product in in ths page -->

            @foreach($products as $product)
            <div class="col-sm-3 card hover">
               
                <img src="{{ $product->photo }}" class="card-img-top" alt="..." width="100" height="233">
                <div class="card-body">
                       <h4>{{ $product->name }}</h4>
                            
                            <p><strong>Price: </strong> {{ $product->price }}$</p>
                            <p class="btn-holder">
                            <a href="{{ url('add-to-cart/'.$product->id) }}" class="btn btn-block text-center text-light" role="button">Add to cart</a> </p>
                </div>
            </div>
                    
                
            @endforeach

        </div><!-- End row -->

    </div>

    <style>

    .btn-block{
        background:light;
        transition: 1.5s ease;
        position:relative;
        bottom:-10
    }
    .card:hover a{
        background:green;
        transition: 1.5s ease;
        transform: translate(-.1%, -.1%);
        
    }
    .hover:hover {

  box-shadow: 0 2px 5px 0 , 0 2px 10px 0 rgba(0, 0, 0, 0.12);
}
    </style>

@endsection


9. make a template to name card.blade.php

@extends('layout')

@section('title', 'Cart')

@section('content')


    <table id="cart" class="table table-hover table-condensed">
        <thead>
        <tr>
            <th style="width:50%">Product</th>
            <th style="width:10%">Price</th>
            <th style="width:8%">Quantity</th>
            <th style="width:22%" class="text-center">Subtotal</th>
            <th style="width:10%"></th>
        </tr>
        </thead>
        <tbody>

        <?php $total = 0 ?>
<!-- by this code session get all product that user chose -->
        @if(session('cart'))
            @foreach(session('cart') as $id => $details)

                <?php $total += $details['price'] * $details['quantity'] ?>

                <tr>
                    <td data-th="Product">
                        <div class="row">
                            <div class="col-sm-3 hidden-xs"><img src="{{ $details['photo'] }}" width="100" height="100" class="img-responsive"/></div>
                            <div class="col-sm-9">
                                <h4 class="nomargin">{{ $details['name'] }}</h4>
                            </div>
                        </div>
                    </td>
                    <td data-th="Price">${{ $details['price'] }}</td>
                    <td data-th="Quantity">
                        <input type="number" value="{{ $details['quantity'] }}" class="form-control quantity" />
                    </td>
                    <td data-th="Subtotal" class="text-center">${{ $details['price'] * $details['quantity'] }}</td>
                    <td class="actions" data-th="">
                    <!-- this button is to update card -->
                        <button class="btn btn-info btn-sm update-cart" data-id="{{ $id }}"><i class="fa fa-refresh"></i></button>
                       <!-- this button is for update card -->
                        <button class="btn btn-danger btn-sm remove-from-cart delete" data-id="{{ $id }}"><i class="fa fa-trash-o"></i>bhh</button>
                    </td>
                </tr>
            @endforeach
        @endif

        </tbody>
        <tfoot>
       
        <tr>
            <td><a href="{{ url('/') }}" class="btn btn-warning"><i class="fa fa-angle-left"></i> Continue Shopping</a></td>
            <td colspan="2" class="hidden-xs"></td>
            <td class="hidden-xs text-center"><strong>Total ${{ $total }}</strong></td>
        </tr>
        </tfoot>
    </table>

@endsection


@section('scripts')


    <script type="text/javascript">
// this function is for update card
        $(".update-cart").click(function (e) {
           e.preventDefault();

           var ele = $(this);

            $.ajax({
               url: '{{ url('update-cart') }}',
               method: "patch",
               data: {_token: '{{ csrf_token() }}', id: ele.attr("data-id"), quantity: ele.parents("tr").find(".quantity").val()},
               success: function (response) {
                   window.location.reload();
               }
            });
        });

        $(".remove-from-cart").click(function (e) {
            e.preventDefault();

            var ele = $(this);

            if(confirm("Are you sure")) {
                $.ajax({
                    url: '{{ url('remove-from-cart') }}',
                    method: "DELETE",
                    data: {_token: '{{ csrf_token() }}', id: ele.attr("data-id")},
                    success: function (response) {
                        window.location.reload();
                        
                    }
                });
            }
        });

    </script>

@endsection


   

   		

      
