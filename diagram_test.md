```mermaid
graph LR
  linkStyle default fill:#ffffff

  subgraph diagram ["TAX Receipts System - TAX Receipts API - Components"]
    style diagram fill:#ffffff,stroke:#ffffff

    subgraph 4 ["TAX Receipts API"]
      style 4 fill:#ffffff,stroke:#25517e,color:#25517e

      5["<div style='font-weight: bold'>Gis.Receipts.API</div><div style='font-size: 70%; margin-top: 0px'>[Component]</div>"]
      style 5 fill:#3674b5,stroke:#25517e,color:#ffffff
      6["<div style='font-weight: bold'>Gis.Receipts.Infrastructure</div><div style='font-size: 70%; margin-top: 0px'>[Component]</div>"]
      style 6 fill:#3674b5,stroke:#25517e,color:#ffffff
      7["<div style='font-weight: bold'>Gis.Receipts.Applications</div><div style='font-size: 70%; margin-top: 0px'>[Component]</div>"]
      style 7 fill:#3674b5,stroke:#25517e,color:#ffffff
      8["<div style='font-weight: bold'>Gis.Receipts.Domain</div><div style='font-size: 70%; margin-top: 0px'>[Component]</div>"]
      style 8 fill:#3674b5,stroke:#25517e,color:#ffffff
    end

    5-. "<div>reference</div><div style='font-size: 70%'></div>" .->6
    5-. "<div>reference</div><div style='font-size: 70%'></div>" .->8
    6-. "<div>reference</div><div style='font-size: 70%'></div>" .->7
    7-. "<div>reference</div><div style='font-size: 70%'></div>" .->8
  end
```
