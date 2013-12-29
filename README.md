# openpay


##Description

ruby client for Openpay API services (version 1.0.0)

This is a ruby client implementing the payment services for Openpay at openpay.mx

For more information about Openpay visit: http://openpay.mx/
For the full Openpay API documentation take a look at: http://docs.openpay.mx/

## Installation

   Add the following line to your Gem file

   # gem install --source https://code.stripe.com stripe

    gem 'openpay'

Update your bundle:

    $ bundle

Or install it from the command line:

    $ gem install openpay-1.0.0.gem

##Requirements

    * ruby 1.8.7 or higher

## Usage


### Initialization
```ruby
require 'openpay'


#merchant and private key
merchant_id='mywvupjjs9xdnryxtplq'
private_key='sk_92b25d3baec149e6b428d81abfe37006'


#An openpay resource factory instance is created out of the OpenpayApi
#it  points to the development environment  by default.
openpay=OpenpayApi.new(merchant_id,private_key)

#To enable production mode you should pass a third argument as true.
#openpay_prod=OpenPayApi.new(merchant_id,private_key,true)
 ```

The openpay factory instance is in charge to generate the required resources through a factory method (create).
Resource classes should be initialized using the factory method as described below.

 ```ruby
#creating an instance for each available resource
bankaccounts=openpay.create(:bankaccounts)
cards=openpay.create(:cards)
charges=openpay.create(:charges)
customers=openpay.create(:customers)
fees=openpay.create(:fees)
payouts=openpay.create(:payouts)
plans=openpay.create(:plans)
subscriptions=openpay.create(:subscriptions)
transfers=openpay.create(:transfers)
```

According to the current version of the Openpay API the available resources are:

- *bankaccounts*
- *cards*
- *charges*
- *customers*
- *fees*
- *payouts*
- *plans*
- *subscriptions*
- *transfers*

Each rest resource exposed in the rest Openpay API is represented by a class in this ruby API, being **OpenpayResource** the base class.


### Implementation
 Each resource depending of its structure and available methods, will have one or more of the methods described under the methods subsection.
 Below a short description about the implementation high level details. For detailed documentation take a look a the openpay api documentation.


#### Arguments
Given most resources belong, either to a merchant or a customer, the api was designed taking this in consideration, so:

The first argument represent the json/hash object, while the second argument which is optional represents the **customer_id**.
So if  just one argument is provided the action will be performed at the merchant level,
but if the second argument is provided passing the customer_id, the action will be performed at the customer level.


The following illustrates the api design.

 ```ruby
#Merchant
hash_out=open_pay_resource.create(hash_in)
json_out=open_pay_resource.create(json_in)


#Customer
hash_out=open_pay_resource.create(hash_in,customer_id)
json_out=open_pay_resource.create(json_in,customer_id)

 ```


####  Methods Inputs/Outputs

This api supports both ruby hashes and json strings as inputs and outputs. (See previous example)
If a ruby hash is passed in as in input, a hash will be returned as the method output.
if a json string is passed in as an input, a json string will be returned as the method function output.

This code excerpt from a specification demonstrates how you can use hashes and json strings  interchangeably

Methods without inputs will return a ruby hash.

```ruby
it 'creates a fee using a json message' do
  #create new customer
  customer_hash= FactoryGirl.build(:customer)
  customer=@customers.create(customer_hash)

  #create customer card   , using factory girl to build the hash for us
  card_hash=FactoryGirl.build(:valid_card)
  card=@cards.create(card_hash, customer['id'])

  #create charge
  charge_hash=FactoryGirl.build(:card_charge, source_id: card['id'], order_id: card['id'], amount: 4000)
  charge=@charges.create(charge_hash, customer['id'])

  #create customer fee , using json as input, we get json as ouput
  fee_json =%^{"customer_id":"#{customer['id']}","amount":"12.50","description":"Cobro de Comisión"}^
  expect(@fees.create(fee_json)).to have_json_path('amount')
end
```

Here you can see how the card hash representation looks like.

```ruby
require 'pp'
pp card_hash   =>

{:bank_name=>"visa",
:holder_name=>"Vicente Olmos",
:expiration_month=>"09",
:card_number=>"4111111111111111",
:expiration_year=>"14",
:bank_code=>"bmx",
:cvv2=>"111",
:address=>
{:postal_code=>"76190",
:state=>"QRO",
:line1=>"LINE1",
:line2=>"LINE2",
:line3=>"LINE3",
:country_code=>"MX",
:city=>"Queretaro"}}
```

Next, how we can construct  the preceding hash using FactoryGirl.

FactoryGirl was used to build up our test suite, to facilitate hash construction.
It  may help you at as well at your final implementation if you decide to use hashes.
(more examples at test/Factories.rb)

```ruby

FactoryGirl.define do
  factory :valid_card, class:Hash do
        bank_name  'visa'
        holder_name 'Vicente Olmos'
        expiration_month '09'
        card_number '4111111111111111'
        expiration_year '14'
        bank_code 'bmx'
        cvv2  '111'
       address {{
           postal_code: '76190',
           state: 'QRO',
           line1: 'LINE1',
           line2: 'LINE2',
           line3: 'LINE3',
           country_code: 'MX',
           city: 'Queretaro',
       }}
    initialize_with { attributes }
  end
```


####API Methods

This ruby API standardize the method names across all different resources using the *create*,*get*,*update* and *delete* verbs.

For full method documentation take a look at:   http://docs.openpay.mx/

The test suite at test/spec is a good source of reference.

#####create

   Creates the given resource
 ```ruby
     open_pay_resource.create(representation,customer_id=nil)
 ```


#####get

   Gets an instance of a  given resource

```ruby
open_pay_resource.get(object_id,customer_id=nil)
```


#####update

   Updates an instance of a given resource

```ruby
open_pay_resource.update(representation,customer_id=nil)
```

#####delete

  Deletes an instance of the given resource

```ruby
open_pay_resource.delete(object_id,customer_id=nil)
```

#####all
   Returns an array of all instances of a resource
```ruby
open_pay_resource.all(customer_id=nil)
```

#####each
   Returns a block for each instance resource
```ruby
open_pay_resource.each(customer_id=nil)
 ```

#####delete_all(available only under the development environment)

   Deletes all instances of the given resource

```ruby
#in case this method is executed under the production environment an OpenpayException will be raised.
open_pay_resource.delete_all(customer_id=nil)
```


## Exceptions

This API generates 3 different Exception classes

- *OpenpayApiError*
     Generic base api exception class, for generic api exceptions.

     Example:

 ```ruby

         merchant_id='santa'
         private_key='invalid'

         openpay=OpenpayApi.new(@merchant_id, @private_key)
         customers=@openpay.create(:customers)


          begin
             @customers.get('23444422211')
          rescue OpenpayApiConnectionError => e
             expect(e.http_code).to be 401
             expect(e.error_code).to be 1002
             expect(e.description).to match 'The api key or merchant id are invalid.'
             expect(e.json_body).to have_json_path('category')
           end
 ```

- *OpenpayApiConnectionError*

     - Connection Errors.
     - Authentication Error.
     - SSL Errors.

      Example:
 ```ruby

         merchant_id='santa'
         private_key='invalid'

         openpay=OpenpayApi.new(@merchant_id, @private_key)
         customers=@openpay.create(:customers)


          begin
             @customers.get('23444422211')
          rescue OpenpayApiConnectionError => e
             expect(e.http_code).to be 401
             expect(e.error_code).to be 1002
             expect(e.description).to match 'The api key or merchant id are invalid.'
             expect(e.json_body).to have_json_path('category')
           end
 ```

- OpenpayApiRequestError

  ```ruby
  email='foo'
  customer_hash = FactoryGirl.build(:customer, email: email)
  begin
    @customers.create(customer_hash)
  rescue OpenpayApiTransactionError => e
    #should have the corresponding attributes coming from the json message
    expect(e.http_code).to be 400
    expect(e.error_code).to be 1001
    expect(e.description).to match 'email\' not a well-formed email address'
    expect(e.json_body).to have_json_path('category')
  end
  ```
These exceptions have the following attributes:


        #category  "request"
        #description"  "email' not a well-formed email address"
        #http_code":400"
        #error_code":1001


    #En el caso de listar un objeto no existente una excepcion de tipo RestClient::ResourceNotFound sera lanzada

      expect { @cards.all('111111') } .to raise_exception   RestClient::ResourceNotFound

Al generarse una exepcion se genera tambien un warning, si tienes acceso a la consola, podras ver ahi tus mensajes de errror en forma de warnings


[1] https://github.com/rest-client/rest-client
[2] http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html


## More information

For more use cases take a look at the test/spec folder

1.  http://docs.openpay.mx/
