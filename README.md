

## Ali_Khatami_solidity_09(learning from the video of Patrick Collins)
### Importing from Github and NPM

Given code below

```
//SPDX-License-Identifier:MIT

pragma solidity ^0.8.8;

import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract akrkFundMe  {

    uint256 public minimumUSD=50;

// we have use payable keyword with the function below to make it payable with any native blockchain currency
    function fund() public payable {

        require(msg.value >= minimumUSD , "Not send enough");// to get accees to the 'value' at deploy at run transaction tab
        //1e18 is 1 ether, here the value must be greater than 1 ether
        //require is a checker it checks whether the value is greater than 1 ether
        // if not it is going to revert with an error messsage shown above

    }// To send money. We made it public so that anybody can call it

  //The function below is created so that we can get price of ethereum in terms of USD , so that we can convert msg.value to USD
//Both functions are public because we can do whatever we want with them
//By using the getPrice() function below we are going to interact with contract outside of our project

    function getPrice() public {

        //ABI
        //Address 0x694AA1769357215DE4FAC081bf1f309aDC325306\
        // below we we create AggregatorV3Interface object called priceFeed equal to AggregatorV3Interface contract at address 0x694AA1769357215DE4FAC081bf1f309aDC325306
        // this address 0x694AA1769357215DE4FAC081bf1f309aDC325306 will have all the functinality at AggregatorV3Interface
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        //below we will call latestRoundData() function of AggregatorV3Interface on price feed
        (, int price, , , )=priceFeed.latestRoundData();

        return price * 1e10; 

        
    }

    //below we have created a new function

    function getVerion() public view returns (uint256)
    {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306); //it is of type 'AggregatorV3Interface'
        return priceFeed.version();

    }

    function getConversionRate()  public {}
    

}
```


In the above code instead of directly adding the code we can import directly from github or what is called an NPM package <br>

Remix ethereum is smart enogh to know that ```@chainlink/contracts/``` is referring NPM package @chainlink/contracts/



![f55](https://user-images.githubusercontent.com/89090776/235946346-0e8e022e-3766-449f-adc2-fdafa62c550c.jpg)
Figure1: If we search it on google by typing '@chainlink/contracts' we can find it.<br>

NPM package is  apckage manager and keep versions and keep versions of different contracts for us to directly import into our code bases

![f56](https://user-images.githubusercontent.com/89090776/235948717-e87de151-9dc6-44eb-ab46-f2d21320b4ed.jpg)
Figure2:@chainlink/contracts is created directly from Chainlink Github reposatory<br>


### Floating point Math in solidity

give in ths code below from github that in AggregatorV3Interface.sol

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface AggregatorV3Interface {
  function decimals() external view returns (uint8);

  function description() external view returns (string memory);

  function version() external view returns (uint256);

  function getRoundData(uint80 _roundId)
    external
    view
    returns (
      uint80 roundId,
      int256 answer,
      uint256 startedAt,
      uint256 updatedAt,
      uint80 answeredInRound
    );

  function latestRoundData()
    external
    view
    returns (
      uint80 roundId,
      int256 answer,
      uint256 startedAt,
      uint256 updatedAt,
      uint80 answeredInRound
    );
}

```


```latestRoundData()``` function does not return a single variable but a whole bunch of different variables


![f57](https://user-images.githubusercontent.com/89090776/236627807-35bfac06-fefa-4552-ac46-aad61777330a.jpg)
figure3: and that is what we are going to return to our contrat<br>


![f58](https://user-images.githubusercontent.com/89090776/236627978-0f647739-6580-4021-9594-6d216cf0ad5b.jpg)
Figure4: We just only need the ```int price``` and remove others except the commas<br>
Here price type is ```int256``` instead of ```uint256``` because some price could be negative<br>
This price gonna be the price of Eth in terms of USD


In the above code at this line ```require(msg.value >= minimumUSD , "Not send enough");``` the  ```msg.value``` have 18 decimels<br>\
because 1 ether equals to 1000000000000000000 Wei where there are about 18 zeroes after the first digit '1'<br>
but price have only 8 zeroes after the decimel place , to match both of them we have to write the code in the following way<br>


![f59](https://user-images.githubusercontent.com/89090776/236629221-b1e48487-1b24-4adc-96d1-d0dc6ea0782a.jpg)
Figure5: this code ```return price * 1e10; ``` will match them both

Another thing we can see in the code that ```msg.value``` is of data type uint256, but price is of data type int256<br>
so to make the data type of price from ```int256``` to ```uint256``` we have to do typecasting in the following way<br>


![f60](https://user-images.githubusercontent.com/89090776/236629820-13644294-af85-402e-8614-d8f8c6c44491.jpg)
Figure6: here we have copnverted the data type of ```int256``` to ```uint256``` and it is known as typecasting<br>
we can't typecast all the data type but ```int256``` and ```uint256``` can be converted between the two<br>

By the ```getPrice()``` function of the above code we are not modifying any state and thus we have made following changes in the code<br>

In solidity multiplication to be done before division<br>

```
//SPDX-License-Identifier:MIT

pragma solidity ^0.8.8;

import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract akrkFundMe  {

    uint256 public minimumUSD=50;

// we have use payable keyword with the function below to make it payable with any native blockchain currency
    function fund() public payable {

        require(msg.value >= minimumUSD , "Not send enough");// to get accees to the 'value' at deploy at run transaction tab
        //1e18 is 1 ether, here the value must be greater than 1 ether
        //require is a checker it checks whether the value is greater than 1 ether
        // if not it is going to revert with an error messsage shown above

    }// To send money. We made it public so that anybody can call it

  //The function below is created so that we can get price of ethereum in terms of USD , so that we can convert msg.value to USD
//Both functions are public because we can do whatever we want with them
//By using the getPrice() function below we are going to interact with contract outside of our project

    function getPrice() public view returns(uint256) {

        //ABI
        //Address 0x694AA1769357215DE4FAC081bf1f309aDC325306\
        // below we we create AggregatorV3Interface object called priceFeed equal to AggregatorV3Interface contract at address 0x694AA1769357215DE4FAC081bf1f309aDC325306
        // this address 0x694AA1769357215DE4FAC081bf1f309aDC325306 will have all the functinality at AggregatorV3Interface
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        //below we will call latestRoundData() function of AggregatorV3Interface on price feed
        (,int256 price,,,)= priceFeed.latestRoundData();

        return uint256(price * 1e10); //typecasting = converting int256 to uint256

        
    }

    //below we have created a new function

    function getVerion() public view returns (uint256)
    {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306); //it is of type 'AggregatorV3Interface'
        return priceFeed.version();

    }
//below is a function by which we gonna convert msg.value from etherium to terms of dollars
//to this function we are going to pass some ethAmount in one side
//on the other side we gonna find how much of that ethAQmount is in terms of USD
    function getConversionRate(uint256 ethAmount)  public view returns (uint256) {

        uint256 ethPrice = getPrice(); // at first we gonna call getPrice function to get the price of ethereum

        uint256 ethAmountInUSD= (ethPrice * ethAmount)/1e18;

        return ethAmountInUSD;

       



    }
    

}

```
here we have done a math below to understand the above code easily


let ethPrice be ```3000_000000000000000000``` which is $3000 and ethAmount be ```1_000000000000000000``` which is 1Eth<br>

therefore, uint256 ethAmountInUSD= (3000000000000000000000 *  1_000000000000000000) = 3000_000000000000000000 which is $3000<br>





we have another change in the code to perfectly convert msg.value from etherium to terms of dollars<br>


```
//SPDX-License-Identifier:MIT

pragma solidity ^0.8.8;

import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract akrkFundMe  {

    uint256 public minimumUSD=50 * 1e18; //as getConversionRate() returns 18 zeroes after decimel place

// we have use payable keyword with the function below to make it payable with any native blockchain currency
    function fund() public payable {
     
     //as we want to convert msg.value to USD we made the following changes
        require(getConversionRate(msg.value) >= minimumUSD , "Not send enough");// to get accees to the 'value' at deploy at run transaction tab
        //1e18 is 1 ether, here the value must be greater than 1 ether
        //require is a checker it checks whether the value is greater than 1 ether
        // if not it is going to revert with an error messsage shown above

    }// To send money. We made it public so that anybody can call it

  //The function below is created so that we can get price of ethereum in terms of USD , so that we can convert msg.value to USD
//Both functions are public because we can do whatever we want with them
//By using the getPrice() function below we are going to interact with contract outside of our project

    function getPrice() public view returns(uint256) {

        //ABI
        //Address 0x694AA1769357215DE4FAC081bf1f309aDC325306\
        // below we we create AggregatorV3Interface object called priceFeed equal to AggregatorV3Interface contract at address 0x694AA1769357215DE4FAC081bf1f309aDC325306
        // this address 0x694AA1769357215DE4FAC081bf1f309aDC325306 will have all the functinality at AggregatorV3Interface
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306);
        //below we will call latestRoundData() function of AggregatorV3Interface on price feed
        (,int256 price,,,)= priceFeed.latestRoundData();

        return uint256(price * 1e10); //typecasting = converting int256 to uint256

        
    }

    //below we have created a new function

    function getVerion() public view returns (uint256)
    {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306); //it is of type 'AggregatorV3Interface'
        return priceFeed.version();

    }
//below is a function by which we gonna convert msg.value from etherium to terms of dollars
//to this function we are going to pass some ethAmount in one side
//on the other side we gonna find how much of that ethAQmount is in terms of USD
    function getConversionRate(uint256 ethAmount)  public view returns (uint256) {

        uint256 ethPrice = getPrice(); // at first we gonna call getPrice function to get the price of ethereum

        uint256 ethAmountInUSD= (ethPrice * ethAmount)/1e18;

        return ethAmountInUSD;

       



    }
    

}


```

1e18 is also known as 1 time 10 raise the 18(1 * 10 ** 18)


![f61](https://user-images.githubusercontent.com/89090776/236674218-9284389c-14c4-40a8-b3dc-f633cd99784c.jpg)
Figure7: after we click the deploy button it metamask window pop up and shows the value of transaction<br>

![f62](https://user-images.githubusercontent.com/89090776/236674353-242a1335-e96f-4686-ad3b-8b26d0297160.jpg)
Figure8: the transaction occured successfully<br>

![f63](https://user-images.githubusercontent.com/89090776/236674864-2ac855bb-d715-401c-a026-2a02bd8b4484.jpg)
Figure9: this is our deployed contract<br>


![f64](https://user-images.githubusercontent.com/89090776/236675009-9da4488d-aeaf-4db8-b0fb-e9c8d5d7afcc.jpg)

Figure10: When we hit payable red color ```fund``` button the above error message shows because the value is here ```0 wei```<br>
and it comes due to this line of code ```require(getConversionRate(msg.value) >= minimumUSD , "Not send enough");``` <br>

![f65](https://user-images.githubusercontent.com/89090776/236675183-7b3a14d6-e519-4755-af57-377eec87312f.jpg)
Figure11: to set the value at first we have to go this link ```https://data.chain.link/ethereum/mainnet/crypto-usd/eth-usd``` <br>
here we can see the current value of eth in terms of USD is ```$1,908.56```<br>

here our minimum value is set to $50<br>

therefore 50/1908.56 = 0.026 which is approximately enough<br>








