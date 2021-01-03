# Day 1 - compute_unit Gem - Collect Real Time Metrics 'n' Manage Compute Units (GPU, CPU, and ASICs) for Crypto Mining Rigs -


Written by {% avatar Logicminds %} [Corey Osman](https://github.com/logicminds)

_By day Corey runs a boutique devops consulting company called, [NWOPS, LLC](https://github.com/nwops) where he provides configuration management and devops services through the [Puppet service partner program](https://www.puppet.com).  Additionally, he writes myriads of other bits from random ideas and thoughts. By night Corey works on software for his crypto mining operations [through a group called BlockOps](https://gitlab.com/blockops).  You can find him on the mountain :ski: when not writing software._



## Crypto Mining and Ruby

Let us first discuss the crypto mining tools available for ruby.  Actually, there is nothing to discuss because nothing exists.  One would think that because ruby is so awesome that there would be hordes of mind blowing tools to manage crypto rigs.  But this is not so.  AFAIK, I am the only person writing crypto mining software built with ruby.  I want to change this, which is why I birthed the compute_unit gem.

Many people and professional mining operating systems like [hiveOS](https://hiveos.farm), [MMPOS](https://app.mmpos.eu) and [Ethos](http://ethosdistro.com) use complex bash scripts to scrape similar information from the OS. Worse, is that each mining OS reinvents the wheel with their own scripts.  With the compute_unit gem you can keep it DRY and build more features faster.

In this discussion we focus on crypto mining software as there are gems for other crypto needs.  See references for a list of various tools and libraries related to crypto.


## What's Compute Unit?

The [compute_unit gem](https://gitlab.com/blockops/compute_unit) is a ruby library that searches the linux sysfs file system for compute unit devices such as CPUS, GPUs and other ASIC compute devices. It allows programmatic access to collect real time metrics from the kernel or related driver toolchains. Is meant to be used as a library for future ruby based tools. This library also makes use of OpenCL library and requires the [opencl_ruby_ffi gem](https://github.com/Nanosim-LIG/opencl-ruby).

The features of this library are:

  * Detect all compute devices such as cpu, gpu, and anything else added in the future
  * Provide the ability to control GPU fans
  * Get temperature of units
  * Change or view voltage, core clock, memory clock and other tuning parameters for overclocking
  * Provide inventory data of all the devices with make, model and other important information
  * Provide real-time status information of various data points. Useful for real-time tuning
  * Interact with the compute device via ruby
  * Allow user to easily add new subclasses for additional functionality


## What Compute Unit does not provide

The compute unit gem does not do any of the following:

  * mine crypto
  * manage mining software
  * provide crypto pool data
  * anything crypto related

Compute Unit is merely an abstraction layer to view and manage any type of compute device found on a linux system using ruby.  It tries to stay focused on the hardware itself instead of any single use case. So this means it could be used as a foundation for:

  *  [Machine learning software](https://ankane.org/more-ml-gems)
  *  [Crypto mining rig software](https://crossbelt.blockops.party/)
  *  Crypto mining software
  *  Gather metrics for Sensu, Prometheus, Collectd, and other monitoring tools


## Getting started with Compute Unit
### Installing

Ruby 2.6+ supported.  Not tested under 3.0 yet.

Install using the usual method `gem install compute_unit`


### Update the pci.ids database

The compute unit library uses the [pci.ids database](https://pci-ids.ucw.cz/) for device information.  This means you will need to have the pci.ids file installed on the system first.  This can be done in a [number of ways](https://gitlab.com/blockops/compute_unit/-/blob/master/README.md#usage).

Depending on your bundle path or wherever you configure gem executables to be stored just run `/usr/local/bundle/bin/update_pcidb`

## Usage
Compute Unit uses the [sysfs](https://www.kernel.org/doc/html/latest/PCI/index.html) files under the `/sys` directory on linux.  In order to read and write these files you will need root access.  Any scripts you create or invocation of the library will require running as root or with sudo.

### Listing devices
One of the first things you can do is to discover the devices.  In this example I only return the first device as I have seven devices on this rig.

```ruby
require 'compute_unit'

ComputeUnit.find_all.first.to_h

#=> {:uuid=>"GPU0",
# :gpuId=>"GPU0",
# :syspath=>"/sys/bus/pci/devices/0000:03:00.0",
# :pciLoc=>"0000:03:00.0",
# :name=>"Radeon RX 5600 OEM/5600 XT / 5700/5700 XT",
# :bios=>"113-1E4260U-O4E",
# :subType=>"amdgpu",
# :make=>"AMD",
# :model=>"Radeon RX 5600 OEM/5600 XT / 5700/5700 XT",
# :vendor=>"Sapphire",
# :power=>101,
# :utilization=>0,
# :temperature=>43,
# :status=>1,
# :pstate=>-1,
# :fanSpeed=>1438,
# :type=>:GPU,
# :maxTemp=>nil,
# :mem=>900,
# :cor=>1360,
# :vlt=>750,
# :mem_temp=>82,
# :maxFan=>nil,
# :dpm=>nil,
# :vddci=>nil,
# :maxPower=>nil,
# :ocProfile=>nil,
# :opencl_enabled=>false}
```

Or on the command line you can just run `ruby -r compute_unit -e 'puts ComputeUnit.find_all.map(&:to_h).to_yaml'`


### Calculate Power usage
A quick check to calculate the total power used on a mining rig. Keep in mind these numbers don't represent power measured from the wall, only what the GPU driver is reporting.

`ComputeUnit.find_all.sum {|cu| cu.power } => 454`

### Setting the fan speed
Set the fan to 60% on the first GPU, then get the rpm of the fan.

```ruby
ComputeUnit.find_all.first.set_fan_limit(60)
# INFO - ComputeUnit: GPU0 current fan set to 60 percent
#=> 60

ComputeUnit.find_all.first.fan
#=> 2141
```

**NOTE** You could also set the fan speed on all devices in a similar manner.


### Dump to a table with OpenCL enabled
Because [compute_unit](https://gitlab.com/blockops/compute_unit) returns the raw data points as a hash you can dump this to a table for enhanced command line output.
Make sure you have the table_print gem installed first. `gem install table_print`

In this example we only want GPUs to be displayed so we use the ComputeUnit::Gpu class.

```ruby
require 'table_print'
require 'compute_unit/gpu'

data = ComputeUnit::Gpu.find_all(true).map do |cu|
  {
    index: cu.uuid,
    power: cu.power,
    temp: cu.temp,
    vendor: cu.vendor,
    model: cu.model,
    bios: cu.bios
  }
end
tp data

# =>
# INDEX | POWER | TEMP | VENDOR   | MODEL                 | BIOS
# ------|-------|------|----------|-----------------------|----------------
# GPU0  | 100   | 43   | Sapphire | AMD Radeon RX 5700 XT | 113-1E4260U-O4E
# GPU1  | 99    | 43   | Sapphire | AMD Radeon RX 5700 XT | 113-1E4260U-O4E
# GPU2  | 97    | 40   | Sapphire | AMD Radeon RX 5700 XT | 113-1E4260U-O4E
# GPU3  | 98    | 47   | Sapphire | AMD Radeon RX 5700 XT | 113-1E4260U-O4E
```


## Tools using Compute Unit
While [this gem](https://rubygems.org/gems/compute_unit) is new, it is ready to be consumed by any future idea you might have.  Additionally, since I am crypto miner and ruby enthusiast. I built an entire command line tool for managing crypto mining rigs in ruby.  My love for the command line, simplicity of ruby and mining crypto forced me into crafting a specialized tool and library for mining crypto.  The main command line tool is called [Crossbelt](https://crossbelt.blockops.party) (not open-sourced).  The Crossbelt tool packs many features such as configuring the rig, status info, metrics, profit/loss statements, coin/algorithm speculation for profitability analysis and much more. The examples you see below formulate rig information for table output from data gathered by the [compute_unit library](https://gitlab.com/blockops/compute_unit).

Profits are good and my house is kept warm all winter long.

### Crossbelt Profit Example


```
$ crossbelt cu profit -p 0.1 --poff=30

INDEX | NAME                           | POWER | HASH_RATE | RATIO | HOURLY_COST | HOURLY_EARNINGS | KWH_COST
------|--------------------------------|-------|-----------|-------|-------------|-----------------|---------|
0     | Radeon RX 5600 OEM/5600 XT ... | 128   | 54.5 Mh   | 8.468 | 0.5 µBTC    | 4.0 µBTC        | 3.7 µBTC
1     | Radeon RX 580                  | 91    | 30.26 Mh  | 6.5   | 0.3 µBTC    | 2.2 µBTC        | 3.7 µBTC
2     | Nitro+ Radeon RX 570/580/590   | 113   | 30.58 Mh  | 5.31  | 0.4 µBTC    | 2.2 µBTC        | 3.7 µBTC
3     | Radeon RX 5600 OEM/5600 XT ... | 129   | 54.48 Mh  | 8.292 | 0.5 µBTC    | 4.0 µBTC        | 3.7 µBTC
4     | RX 5700 XT RAW II              | 146   | 54.51 Mh  | 7.37  | 0.5 µBTC    | 4.0 µBTC        | 3.7 µBTC
5     | Radeon RX 5600 OEM/5600 XT ... | 122   | 54.44 Mh  | 8.844 | 0.5 µBTC    | 4.0 µBTC        | 3.7 µBTC
6     | Radeon RX 5600 OEM/5600 XT ... | 124   | 54.39 Mh  | 8.63  | 0.5 µBTC    | 4.0 µBTC        | 3.7 µBTC
      | Total                          | 853   | 333.16 Mh | 7.63  | 3.2 µBTC    | 24.3 µBTC       | 3.7 µBTC
```

### Crossbelt Status Example

```
$ crossbelt cu status

INDEX | NAME                           | CORE_CLOCK | MEMORY_CLOCK | POWER | FAN  | CORE_VOLT | TEMP | MEM_TEMP | STATUS
------|--------------------------------|------------|--------------|-------|------|-----------|------|----------|-------
GPU0  | Radeon RX 5600 OEM/5600 XT ... | 1365       | 900          | 98    | 2401 | 731       | 49   | 82       | 0
GPU1  | Radeon RX 580                  | 1190       | 2000         | 61    | 1166 | 862       | 66   | 0        | 0
GPU2  | Nitro+ Radeon RX 570/580/590   | 1190       | 2100         | 83    | 1659 | 862       | 51   | 0        | 0
GPU3  | Radeon RX 5600 OEM/5600 XT ... | 1365       | 900          | 99    | 2713 | 737       | 51   | 84       | 0
GPU4  | RX 5700 XT RAW II              | 1365       | 900          | 116   | 1614 | 712       | 53   | 82       | 0
GPU5  | Radeon RX 5600 OEM/5600 XT ... | 1365       | 900          | 93    | 1326 | 737       | 56   | 84       | 0
GPU6  | Radeon RX 5600 OEM/5600 XT ... | 1375       | 900          | 94    | 1587 | 712       | 56   | 82       | 0
```


If you develop a tool using [compute_unit](https://gitlab.com/blockops/compute_unit), please reach out and let me know and I'll list it in the readme.


## Looking forward

Python has long been the champion behind machine learning.  While C, C++, and Go are the stardard for existing crypto software.  But where does Ruby play a role in all this?

Ruby is the goto for web frameworks, cofiguration management, and exceptional programmer efficiency.  However, Ruby needs to be a part of machine learning, crypto and other other areas igniting the scene today.  If the majority of the existing tools
are C based libraries we should only have to wrap these with FFI to gain the functionality those compiled libraries provide.  With this in mind  [Machine Learning](https://ankane.org/more-ml-gems), and [Ruby OpenCL library](https://github.com/Nanosim-LIG/opencl-ruby)
are just the starting point to make Ruby another choice for crypto software where simplicity matters most.

## Looking back


If you are reading this, some historical information recorded on this day.

- Bitcoin (BTC): $28,201.00
- Ether (ETH): $751.00, Ethereum 2 migration and staking is underway
- Monero (XMR): $160.00
- Decred (DCR): $37.00
- Ripple (XRP): $0.22 Caught up in a [SEC scandal](https://www.sec.gov/news/press-release/2020-338) and everyone dumped it.



## Find Out More

### More Crypto and Ruby References

- [Crossbelt mining tool](https://crossbelt.blockops.party)
- [Ruby OpenCL library](https://github.com/Nanosim-LIG/opencl-ruby)
- [CryptoCompare API wrapper](https://github.com/alexanderdavidpan/cryptocompare)
- [Machine Learning](https://ankane.org/more-ml-gems) - might be useful for creating ruby mining software
- [Github search for ruby crypto gems](https://github.com/search?q=ruby+crypto&type=Repositories)


### References

- Home  :: [gitlab.com/blockops/compute_unit](https://gitlab.com/blockops/compute_unit)
- Gem   :: [compute_unit](https://rubygems.org/gems/compute_unit)
- Docs  :: [compute_unit](http://rubydoc.info/gems/compute_unit)
