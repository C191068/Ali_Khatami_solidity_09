

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
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306)
        //below we will call latestRoundData() function of AggregatorV3Interface on price feed
        (, int price, , , )=priceFeed.latestRoundData();

        return price * 1e10; 

        
    }

    //below we have created a new function

    function getVerion() public view returns (uint256)
    {
        AggregatorV3Interface priceFeed = AggregatorV3Interface(0x694AA1769357215DE4FAC081bf1f309aDC325306) //it is of type 'AggregatorV3Interface'
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




