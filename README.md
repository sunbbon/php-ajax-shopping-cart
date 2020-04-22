In this post, we will see a simple PHP Ajax example for making a shopping cart application using Bootstrap Popover. This PHP Ajax shopping cart application is explicitly kept easy and as less as available. If you want to get this script then You can just download this script and simply customize it as per your requirement. Here, we have load a list of items for shopping cart from the mysql database by using Jquery Ajax. For every item, we can enter item quantity as per our need and simply add into cart by using Ajax with PHP. The cart details will be stored in the PHP session and which we will fetch by using Ajax function call. Here we will display shopping cart details into Bootstrap popover. So we have to just click on anchor tag and Bootstrap popover will be slide down on web page with shopping cart details. In this shopping cart we can remove single product item also and we can also clear whole shopping cart by single click using Ajax, so here ajax will send ajax request to php script and it will clear session item, so whole shopping cart will be empty.


// From:  https://www.webslesson.info/2018/04/shopping-cart-by-using-bootstrap-popover-with-ajax-php.html

// Demo:  http://demo.webslesson.info/bootstrap-popover-cart/

Bootstrap Popover for Shopping Cart

Here we have use Bootstrap popover plugin for display shopping cart details. So first we have to write HTML code for Bootstrap popover. Here we have use Bootstrap nav class for header menu and on this menu we have display Bootstrap popover plugin link. So when we have click on that link then popover will be slide down on web page in which we can see cart details.


<div class="container">
   <br />
   <h3 align="center">PHP Ajax Shopping Cart by using Bootstrap Popover</h3>
   <br />
   <nav class="navbar navbar-default" role="navigation">
    <div class="container-fluid">
     <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target=".navbar-collapse">
      <span class="sr-only">Menu</span>
      <span class="glyphicon glyphicon-menu-hamburger"></span>
      </button>
      <a class="navbar-brand" href="/">Webslesson</a>
     </div>
     
     <div id="navbar-cart" class="navbar-collapse collapse">
      <ul class="nav navbar-nav">
       <li>
        <a id="cart-popover" class="btn" data-placement="bottom" title="Shopping Cart">
         <span class="glyphicon glyphicon-shopping-cart"></span>
         <span class="badge"></span>
         <span class="total_price">$ 0.00</span>
        </a>
       </li>
      </ul>
     </div>
     
    </div>
   </nav>
   <div id="popover_content_wrapper" style="display: none">
    <span id="cart_details"></span>
    <div align="right">
     <a href="#" class="btn btn-primary" id="check_out_cart">
     <span class="glyphicon glyphicon-shopping-cart"></span> Check out
     </a>
     <a href="#" class="btn btn-default" id="clear_cart">
     <span class="glyphicon glyphicon-trash"></span> Clear
     </a>
    </div>
   </div>

   <div id="display_item">


   </div>
   
  </div>


 

For Activate Bootstrap popover we have write following jquery code, so when we have click on anchor tag with id cart-popover then popover will be slide down on web page with shopping cart details.


$('#cart-popover').popover({
  html : true,
        container: 'body',
        content:function(){
         return $('#popover_content_wrapper').html();
        }
});


Load Item for Shopping Cart

Now we have move to load product on web page for shopping cart by using PHP Ajax. For this we have make one function with Ajax request. This function has been called when page has been load on web browser this function will be called and it will display product on web page under division tag with id display_item.


<!-- index.php !-->

load_product();

function load_product()
{
              $.ajax({
   url:"fetch_item.php",
   method:"POST",
   success:function(data)
   {
    $('#display_item').html(data);
   }
  });
}


 


<?php

//fetch_item.php

include('database_connection.php');

$query = "SELECT * FROM tbl_product ORDER BY id DESC";

$statement = $connect->prepare($query);

if($statement->execute())
{
 $result = $statement->fetchAll();
 $output = '';
 foreach($result as $row)
 {
  $output .= '
  <div class="col-md-3" style="margin-top:12px;">  
            <div style="border:1px solid #333; background-color:#f1f1f1; border-radius:5px; padding:16px; height:350px;" align="center">
             <img src="images/'.$row["image"].'" class="img-responsive" /><br />
             <h4 class="text-info">'.$row["name"].'</h4>
             <h4 class="text-danger">$ '.$row["price"] .'</h4>
             <input type="text" name="quantity" id="quantity' . $row["id"] .'" class="form-control" value="1" />
             <input type="hidden" name="hidden_name" id="name'.$row["id"].'" value="'.$row["name"].'" />
             <input type="hidden" name="hidden_price" id="price'.$row["id"].'" value="'.$row["price"].'" />
             <input type="button" name="add_to_cart" id="'.$row["id"].'" style="margin-top:5px;" class="btn btn-success form-control add_to_cart" value="Add to Cart" />
            </div>
        </div>
  ';
 }
 echo $output;
}


?>



View Demo




 


Fetch Shopping Cart Details

Now we want to make on jquery function which will fetch shopping cart details which we have store in PHP Session. So in this function we have use Ajax request which send request to PHP script and that script will fetch shopping cart details which we have store into Session variable, then that details will be converted into html table format and send back to Ajax request and it will display that cart details under Bootstrap popover by using Jquery. This jquery function will be called when page has been load on browser.


<!-- index.php !-->
<script>  
$(document).ready(function(){

load_cart_data();

function load_cart_data()
{
  $.ajax({
   url:"fetch_cart.php",
   method:"POST",
   dataType:"json",
   success:function(data)
   {
    $('#cart_details').html(data.cart_details);
    $('.total_price').text(data.total_price);
    $('.badge').text(data.total_item);
   }
  });
}
</script>


 


<?php

//fetch_cart.php

session_start();

$total_price = 0;
$total_item = 0;

$output = '
<div class="table-responsive" id="order_table">
 <table class="table table-bordered table-striped">
  <tr>  
            <th width="40%">Product Name</th>  
            <th width="10%">Quantity</th>  
            <th width="20%">Price</th>  
            <th width="15%">Total</th>  
            <th width="5%">Action</th>  
        </tr>
';
if(!empty($_SESSION["shopping_cart"]))
{
 foreach($_SESSION["shopping_cart"] as $keys => $values)
 {
  $output .= '
  <tr>
   <td>'.$values["product_name"].'</td>
   <td>'.$values["product_quantity"].'</td>
   <td align="right">$ '.$values["product_price"].'</td>
   <td align="right">$ '.number_format($values["product_quantity"] * $values["product_price"], 2).'</td>
   <td><button name="delete" class="btn btn-danger btn-xs delete" id="'. $values["product_id"].'">Remove</button></td>
  </tr>
  ';
  $total_price = $total_price + ($values["product_quantity"] * $values["product_price"]);
  $total_item = $total_item + 1;
 }
 $output .= '
 <tr>  
        <td colspan="3" align="right">Total</td>  
        <td align="right">$ '.number_format($total_price, 2).'</td>  
        <td></td>  
    </tr>
 ';
}
else
{
 $output .= '
    <tr>
     <td colspan="5" align="center">
      Your Cart is Empty!
     </td>
    </tr>
    ';
}
$output .= '</table></div>';
$data = array(
 'cart_details'  => $output,
 'total_price'  => '$' . number_format($total_price, 2),
 'total_item'  => $total_item
); 

echo json_encode($data);


?>


Add Items into Shopping Cart

Now we have completed working on display product on web page that means we have make product catalog on web page and we have also make jquery ajax function for fetch shopping cart details from PHP Session variable display on web page. On product catalog we have put 'Add to Cart' button on each product, so by clicking on that button we can add items into shopping cart by using Ajax with PHP. So, When we have click on the 'Add to cart' button, then we add particular product id and its quantity into a shopping cart. For storing shopping cart details we have use PHP SESSION variable. In Following code you can understand how to add items into shopping cart session variable and if we have add single item multiple times then it's quantity will be increment. Here we have use Ajax request with JQuery PHP for add item into cart.


<!--- index.php ---!>
<script>  
$(document).ready(function(){

 $(document).on('click', '.add_to_cart', function(){
  var product_id = $(this).attr("id");
  var product_name = $('#name'+product_id+'').val();
  var product_price = $('#price'+product_id+'').val();
  var product_quantity = $('#quantity'+product_id).val();
  var action = "add";
  if(product_quantity > 0)
  {
   $.ajax({
    url:"action.php",
    method:"POST",
    data:{product_id:product_id, product_name:product_name, product_price:product_price, product_quantity:product_quantity, action:action},
    success:function(data)
    {
     load_cart_data();
     alert("Item has been Added into Cart");
    }
   });
  }
  else
  {
   alert("lease Enter Number of Quantity");
  }
 });

</script>


 


<?php

//action.php

session_start();

if(isset($_POST["action"]))
{
 if($_POST["action"] == "add")
 {
  if(isset($_SESSION["shopping_cart"]))
  {
   $is_available = 0;
   foreach($_SESSION["shopping_cart"] as $keys => $values)
   {
    if($_SESSION["shopping_cart"][$keys]['product_id'] == $_POST["product_id"])
    {
     $is_available++;
     $_SESSION["shopping_cart"][$keys]['product_quantity'] = $_SESSION["shopping_cart"][$keys]['product_quantity'] + $_POST["product_quantity"];
    }
   }
   if($is_available == 0)
   {
    $item_array = array(
     'product_id'               =>     $_POST["product_id"],  
     'product_name'             =>     $_POST["product_name"],  
     'product_price'            =>     $_POST["product_price"],  
     'product_quantity'         =>     $_POST["product_quantity"]
    );
    $_SESSION["shopping_cart"][] = $item_array;
   }
  }
  else
  {
   $item_array = array(
    'product_id'               =>     $_POST["product_id"],  
    'product_name'             =>     $_POST["product_name"],  
    'product_price'            =>     $_POST["product_price"],  
    'product_quantity'         =>     $_POST["product_quantity"]
   );
   $_SESSION["shopping_cart"][] = $item_array;
  }
 }
}

?>


Remov Single Item or Clear Cart Details

Here we have make shopping cart in which user can to remove single item from the cart by click on Remove button which can be find on in each row of cart. And We have also allow to user clear all items from cart by click on clear button which user can find under Bootstrap popover shopping cart. When we have click on Remove button, then it will call jquery code and in that we have send Ajax request to PHP script for remove particular item from PHP SESSION variable based on value of product id. For this we have use PHP unset() function for clear single item or all item from cart. In following code you can find how to use Jquery Ajax code for remove single item from cart or remove all items from shopping cart.


<!--- index.php !--->
<script>  
$(document).ready(function(){

 $(document).on('click', '.delete', function(){
  var product_id = $(this).attr("id");
  var action = 'remove';
  if(confirm("Are you sure you want to remove this product?"))
  {
   $.ajax({
    url:"action.php",
    method:"POST",
    data:{product_id:product_id, action:action},
    success:function()
    {
     load_cart_data();
     $('#cart-popover').popover('hide');
     alert("Item has been removed from Cart");
    }
   })
  }
  else
  {
   return false;
  }
 });

 $(document).on('click', '#clear_cart', function(){
  var action = 'empty';
  $.ajax({
   url:"action.php",
   method:"POST",
   data:{action:action},
   success:function()
   {
    load_cart_data();
    $('#cart-popover').popover('hide');
    alert("Your Cart has been clear");
   }
  });
 });
    
});

</script>


 


<?php

//action.php

session_start();

if(isset($_POST["action"]))
{

 if($_POST["action"] == 'remove')
 {
  foreach($_SESSION["shopping_cart"] as $keys => $values)
  {
   if($values["product_id"] == $_POST["product_id"])
   {
    unset($_SESSION["shopping_cart"][$keys]);
   }
  }
 }
 if($_POST["action"] == 'empty')
 {
  unset($_SESSION["shopping_cart"]);
 }
}

?>


So, this is simple tutorial on How to use Bootstrap Popover plugin for make Shopping cart by using PHP Ajax Jquery with Mysql.
