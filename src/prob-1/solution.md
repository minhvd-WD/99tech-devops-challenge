# Problem 1: Too Many Things To Do

## Solution
CLI command   

```$ sed -nr 's/.*"order_id": "([^"]+)".*"symbol": "TSLA".*"side": "sell".*/\1/p' ./transaction-log.txt | xargs -I {} curl -s https://example.com/api/{} -o ./output.txt```

## Explanation
1. `sed -nr 's/.*"order_id": "([^"]+)".*"symbol": "TSLA".*"side": "sell".*/\1/p' ./transaction-log.txt`  
  - Finding lines containing "symbol": "TSLA" & "side": "sell", ensuring only process relevant transactions (selling TSLA).
  - Replacing found lines by order_id value
  - Using flag:
    - `n` to only explicitly printed lines appear in the output
    - `r` to enables extended regular expressions (characters have special meaning unless prefixed with a backslash '\\')

2. `xargs -I {} curl -s https://example.com/api/{} -o ./output.txt`
  - Substituting {} with the extracted order_id in the API URL (defining placeholder with flag `I`).  
  -	Appending the HTTP response to ./output.txt, using flag 
    - `s` for not printing output to STDOUT
    - `o` for printing output to file output.txt