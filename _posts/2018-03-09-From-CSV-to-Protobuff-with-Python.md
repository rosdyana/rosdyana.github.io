---
layout:     post
title:      "From CSV to ProtocolBuffer with Python"
subtitle:   "Converting CSV file to ProtocolBuffer using Python."
date:       2018-03-09 12:37:00
author:     "rosdyana"
header-img: "img/2018-03-09-From-CSV-to-Protobuff-with-Python/Google-protocol-buffer.png"
category:   techblog
tags:       [python, csv, protocol-buffer, data-preprocessing]
---

As we know Protocol buffers are Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data.
Protocol buffer provided smaller, faster and simpler technology. Or we can define it as a way to encoding structured data in an efficient yet extensible format.
Basically, protocol buffer supported by many programming language such as Java, Python, Objective-C, C++ and with proto3 it also supports JavaNano, Ruby, Go and C#.

Protocol buffers have many advantages over XML for serializing structured data.

-   protocol buffers are simpler.
-   protocol buffers are 3 to 10 times smaller.
-   protocol buffers are 20 - 100 times faster.
-   protocol buffers are less ambiguous.
-   generate data access classes that are easier to use programmatically.

| No  |           Advantages           |
| --- | ---------------------------- |
| 1   |       Schemas are awesome      |
| 2   |      Backward compability      |
| 3   |      Less boilerplate code     |
| 4   |  validations and Extensibility |
| 5   | Easy language interoperability |

for these example, we have CSV with structured bellow,

| CompanyCode | CompanyName | Country | Ticker |
|-----------|:-----------:|:-------:|:------:|
| 2377 | China Steel Corp | TW | 2002 TT |
| 2726 | Uni-President Enterprises Corp | TW | 1216 TT |
|  |  |  |  |

then, we would like to serializing those CSV into protocol buffers' format.
we can create a proto file first and save it as company.proto

```
syntax = "proto3";
package crilist;

message CompanyMap {
    string name = 1;
    int64 code = 2;
    string ticker = 4;
    string countryCode = 5;
}

message CompanyList {
    repeated CompanyMap company = 1;
}
```

next, we need to generate classes from proto file for Python.
in case you don't have protocol buffer installed on your machine, please follow this [link]:https://developers.google.com/protocol-buffers/docs/downloads

run this command to generate the classes

```
protoc --proto_path=. --python_out=. company.proto

```

then you will have generated classes with name company_pb2.py

now, the part to read the CSV file and convert it to protocol buffers.

```python

import argparse
import pandas as pd
import company_pb2

def saveToPB(df):
    company_list = company_pb2.CompanyList()
    company = company_list.company.add()
    for i,r in df.iterrows():
        company.name = r['Company_Name']
        company.code = r['U3_Company_Number']
        company.ticker = r['Ticker']
        company.countryCode = r['Country']
        with open('output_pb', "ab") as f:
            f.write(company_list.SerializeToString())

def main():
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter)
    parser.add_argument('-i', '--finput',
                        help='file source to read.', default="input.csv")
    args = parser.parse_args()

    df = pd.read_csv(args.finput)
    saveToPB(df)

if **name** == "**main**":
    main()

```
those python script will read input.csv and save it to protocol buffers with name output_pb.
If you want to stream the protocol buffers, you can use this python script.

```python

# first we need to import the generated classes

import company_pb2

def ListCompany():
    f = open('output_pb', 'rb')
    data = company_pb2.CompanyList()
    data.ParseFromString(f.read())
    f.close()
    print((data))

def main():
    ListCompany()

if **name** == '**main**':
  main()

```

hopes you getting clear about implementation of protocol buffers using python.
