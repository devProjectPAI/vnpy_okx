# OKX trading gateway for VeighNa Evo

Copy from: https://github.com/veighna-global/vnpy_okx

**5 issues Fixed:**

**1. Wrong mode checking:**
   
   orginal code is "TEST", which must be changed to "DEMO" as below:

if server == "DEMO":

    self.simulated = True


self.simulated is used to set header with a field: "x-simulated-trading: 1".

This means that will use OKX simulation account api.


**2. Parameters Error of return:**

   ts, o, h, l, c, vol, _volCcy, _volCcyQuote, _confirm = bar_list

   Orignal code has no such three parameters: _volCcy, _volCcyQuote, _confirm.
   
   Must add these three parameters, even we don't use them.


**3. string to float Error in on_query_contract:**
   
   Add one function "is_float" to avoid exception when call string to float.


**4. Trade Order parameters error in FUTURES order type.**
   
   The orignal code's SPOT order is all right.
   
   But when using the FUTURE order, there is an error returned by okx api : posSide parameter is missing.
   
   So we need add this "posSide" parameter when trade order type is FUTURES.
   
   The "send_order" function's code has been changed to fix it.


**5. FUTURES type order's amount unit error:**
   
   In OKX, the FUTURES type order's amount unit is not 1. It is 0.01.
   
   ( Normal trade order SPOT type, the unit is 1.)
   
   In the orignal code, BTC-USDT trade order:
   
        SPOT order: amount is 1, the order submitted. the amount of order's status is 1.
        
        FUTURES order: amount is 1, the order submitted. the amount of order's status is 0.01.

   So use a constant variable SWAP_FUTURES_VOL_FIX=100 to fix the amount when submitting trade order and querying order's status with FUTURES order type.



## Install

Users can only install ``vnpy_okx`` manually as following command.

```
python3 setup.py build
```
```
python3 setup.py install
```

If there is an permission error when installing, Please try to use:

sudo python3 setup.py install


## A Simple Example

Save this as run.py.

```
from vnpy_evo.event import EventEngine
from vnpy_evo.trader.engine import MainEngine
from vnpy_evo.trader.ui import MainWindow, create_qapp

from vnpy_okx import (
    BinanceSpotGateway,
    BinanceLinearGateway,
    BinanceInverseGateway
)


def main():
    """主入口函数"""
    qapp = create_qapp()

    event_engine = EventEngine()
    main_engine = MainEngine(event_engine)
    main_engine.add_gateway(BinanceSpotGateway)
    main_engine.add_gateway(BinanceLinearGateway)
    main_engine.add_gateway(BinanceInverseGateway)

    main_window = MainWindow(main_engine, event_engine)
    main_window.showMaximized()

    qapp.exec()


if __name__ == "__main__":
    main()
```
