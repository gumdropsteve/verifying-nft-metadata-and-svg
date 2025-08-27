# verifying-nft-metadata-and-svg
This is a simple repo showing how to download/verify the URI of an NFT and download the SVG from that, including an example with a NFT collection deployed on Monad testnet.

# Verifying PropertyNFT Metadata + SVG

These steps show how to fetch, decode, and view the on-chain metadata and SVG for any minted `PropertyNFT`. See the comment below for applied example with first few steps completed (in a way that can be copy/pasted).

---

## 1. Call `tokenURI(tokenId)`

Use `cast` to call the contract and get the metadata URI:

```bash
cast call <PropertyNFT_address> "tokenURI(uint256)" <id> --rpc-url <rpc>
```

The output will look something like this
```bash
0x0000000000000...75248566a526b5130493055464e4a6...00000000000000000000000
```

Your `cast call` output is ABI-encoded. You can extract the human-readable string directly: 
```bash
cast --to-ascii 0x0000000000000...
```

This will return something like
```bash
data:application/json;base64,eyJuYW1lIjogIlByb3BlcnR5ICMwIiwiZGVzY3JpcHRpb24iOiAiVG9rZW5pemVkIFJlYWwgRXN0YXRlIFByb3BlcnR5Li4uIn0=
```


## 2. Decode the Base64 JSON
Strip the prefix data:application/json;base64, and decode the JSON:

```bash
echo '<BASE64_JSON>' | base64 -d > metadata.json
cat metadata.json
```

Example output:
```bash
{
  "name": "Property #0",
  "description": "Tokenized Real Estate Property with on-chain verification...",
  "image": "data:image/svg+xml;base64,PHN2ZyB4bWxucz0..."
}
```

## 3. Decode the Embedded SVG
Take the value of the image field (after data:image/svg+xml;base64,) and decode it:

```bash 
echo '<BASE64_SVG>' | base64 -d > property.svg
```

## 4. View the SVG
Open property.svg in a browser or image viewer to confirm the on-chain rendering.

# Applied Example
Here is an applied example with an NFT on Monad testnet. I figured out how to download the URI and then the SVG from that URI because I needed to verify that the SVG was being created as expected before applying the metadata functions to other NFT contracts within the system being developed. Normally I would have tested this by finding the NFT collection on OpenSea or Magic Eden, but OpenSea no longer supports testnet and the SVG images for the collection were not displaying on Magic Eden. 

## Copy + Paste This Instance
```bash
cast call 0xd1CbAD4275681c70091705A3Dc03D1F7BDe46730 "tokenURI(uint256)" 0 --rpc-url https://testnet-rpc.monad.xyz
```

The output should look something like this (full output can be found [in this gist](https://gist.github.com/gumdropsteve/e75b8e22e171f8d4212ac2605113c084)).
```bash
0x000000...35a6b523159305a4551304f4752485...0000000000000
```

Your `cast call` output is ABI-encoded. You can extract the human-readable string directly: 
```bash
cast --to-ascii 0x00000...
```

The output should look something like this (full output can be found [in this gist](https://gist.github.com/gumdropsteve/db3cf4413dc740f1b2365328dd0537d9)).
```bash
udata:application/json;base64,eyJuYW1lIb3Bl...255In1dfQ==
```

### 2. Decode the json
The commands for this section applied to this example can be found [in this gist](https://gist.github.com/gumdropsteve/4037448012b8eb39224ba82bbd989006).

The output from those commands should look like this:
```bash
{"name": "Property #0", "description": "Tokenized Real Estate Property with on-chain verification and Loot-inspired attributes.", "image": "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaW5ZTWluIG1lZXQiIHZpZXdCb3g9IjAgMCAzNTAgMzUwIj48c3R5bGU+LmJhc2UgeyBmaWxsOiB3aGl0ZTsgZm9udC1mYW1pbHk6IHNlcmlmOyBmb250LXNpemU6IDE0cHg7IH0gLmhlYWRlciB7IGZpbGw6ICNEQUE1MjA7IGZvbnQtZmFtaWx5OiBzZXJpZjsgZm9udC1zaXplOiAxNnB4OyBmb250LXdlaWdodDogYm9sZDsgfSAuc3RhdHVzIHsgZm9udC1zaXplOiAxMnB4OyB9IC5hcHByb3ZlZCB7IGZpbGw6ICMzMkNEMzI7IH0gLnBlbmRpbmcgeyBmaWxsOiAjRkZENzAwOyB9IC5yZWplY3RlZCB7IGZpbGw6ICNGRjYzNDc7IH08L3N0eWxlPjxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9ImJsYWNrIiAvPjx0ZXh0IHg9IjEwIiB5PSIyNSIgY2xhc3M9ImhlYWRlciI+UHJvcGVydHkgIzA8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjQ1IiBjbGFzcz0iYmFzZSBzdGF0dXMgYXBwcm92ZWQiPlN0YXR1czogQXBwcm92ZWQ8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjcwIiBjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PHRleHQgeD0iMTAiIHk9Ijk1IiBjbGFzcz0iYmFzZSI+TG9jYXRpb246IDEyMy4uLjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMTIwIiBjbGFzcz0iYmFzZSI+Q29uZGl0aW9uOiBGYWlyPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNDUiIGNsYXNzPSJiYXNlIj5BZ2U6IEVzdGFibGlzaGVkPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNzAiIGNsYXNzPSJiYXNlIj5GZWF0dXJlczogQmFsY29ueTwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMjIwIiBjbGFzcz0iYmFzZSI+VmFsdWU6ICQ5MDAwMDBNPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIyNDUiIGNsYXNzPSJiYXNlIj5NYXggTFRWOiA4MCU8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjI4MCIgY2xhc3M9ImJhc2UiIHN0eWxlPSJmb250LXNpemU6IDEycHg7Ij5BZGRyZXNzOiAxMjMgVVNEWCBUZXN0IEF2ZSwgVC4uLjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMzA1IiBjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PC9zdmc+", "attributes": [{"trait_type": "Status", "value": "Approved"},{"trait_type": "Type", "value": "Residential"},{"trait_type": "Market Value", "display_type": "number", "value": 900000000000},{"trait_type": "Max LTV", "display_type": "number", "value": 8000},{"trait_type": "Type", "value": "Residential"},{"trait_type": "Location", "value": "123..."},{"trait_type": "Condition", "value": "Fair"},{"trait_type": "Age", "value": "Established"},{"trait_type": "Features", "value": "Balcony"}]}%  
```

### 3. Decode the Embedded SVG
Inside `metadata.json` (the output above), find the `"image": "data:image/svg+xml;base64,PHN2Zy..."`.
Copy only the part after `data:image/svg+xml;base64,`.

What you copy should look like this:
```bash
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaW5ZTWluIG1lZXQiIHZpZXdCb3g9IjAgMCAzNTAgMzUwIj48c3R5bGU+LmJhc2UgeyBmaWxsOiB3aGl0ZTsgZm9udC1mYW1pbHk6IHNlcmlmOyBmb250LXNpemU6IDE0cHg7IH0gLmhlYWRlciB7IGZpbGw6ICNEQUE1MjA7IGZvbnQtZmFtaWx5OiBzZXJpZjsgZm9udC1zaXplOiAxNnB4OyBmb250LXdlaWdodDogYm9sZDsgfSAuc3RhdHVzIHsgZm9udC1zaXplOiAxMnB4OyB9IC5hcHByb3ZlZCB7IGZpbGw6ICMzMkNEMzI7IH0gLnBlbmRpbmcgeyBmaWxsOiAjRkZENzAwOyB9IC5yZWplY3RlZCB7IGZpbGw6ICNGRjYzNDc7IH08L3N0eWxlPjxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9ImJsYWNrIiAvPjx0ZXh0IHg9IjEwIiB5PSIyNSIgY2xhc3M9ImhlYWRlciI+UHJvcGVydHkgIzA8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjQ1IiBjbGFzcz0iYmFzZSBzdGF0dXMgYXBwcm92ZWQiPlN0YXR1czogQXBwcm92ZWQ8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjcwIiBjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PHRleHQgeD0iMTAiIHk9Ijk1IiBjbGFzcz0iYmFzZSI+TG9jYXRpb246IDEyMy4uLjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMTIwIiBjbGFzcz0iYmFzZSI+Q29uZGl0aW9uOiBGYWlyPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNDUiIGNsYXNzPSJiYXNlIj5BZ2U6IEVzdGFibGlzaGVkPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNzAiIGNsYXNzPSJiYXNlIj5GZWF0dXJlczogQmFsY29ueTwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMjIwIiBjbGFzcz0iYmFzZSI+VmFsdWU6ICQ5MDAwMDBNPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIyNDUiIGNsYXNzPSJiYXNlIj5NYXggTFRWOiA4MCU8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjI4MCIgY2xhc3M9ImJhc2UiIHN0eWxlPSJmb250LXNpemU6IDEycHg7Ij5BZGRyZXNzOiAxMjMgVVNEWCBUZXN0IEF2ZSwgVC4uLjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMzA1IiBjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PC9zdmc+
```

Now you need to run the command:
```bash
echo 'PHN2ZyB4bWxucz0...' | base64 -d > property.svg
```

Applied to this example, that command looks like:
```bash
echo 'PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaW5ZTWluIG1lZXQiIHZpZXdCb3g9IjAgMCAzNTAgMzUwIj48c3R5bGU+LmJhc2UgeyBmaWxsOiB3aGl0ZTsgZm9udC1mYW1pbHk6IHNlcmlmOyBmb250LXNpemU6IDE0cHg7IH0gLmhlYWRlciB7IGZpbGw6ICNEQUE1MjA7IGZvbnQtZmFtaWx5OiBzZXJpZjsgZm9udC1zaXplOiAxNnB4OyBmb250LXdlaWdodDogYm9sZDsgfSAuc3RhdHVzIHsgZm9udC1zaXplOiAxMnB4OyB9IC5hcHByb3ZlZCB7IGZpbGw6ICMzMkNEMzI7IH0gLnBlbmRpbmcgeyBmaWxsOiAjRkZENzAwOyB9IC5yZWplY3RlZCB7IGZpbGw6ICNGRjYzNDc7IH08L3N0eWxlPjxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9ImJsYWNrIiAvPjx0ZXh0IHg9IjEwIiB5PSIyNSIgY2xhc3M9ImhlYWRlciI+UHJvcGVydHkgIzA8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjQ1IiBjbGFzcz0iYmFzZSBzdGF0dXMgYXBwcm92ZWQiPlN0YXR1czogQXBwcm92ZWQ8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjcwIiBjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PHRleHQgeD0iMTAiIHk9Ijk1IiBjbGFzcz0iYmFzZSI+TG9jYXRpb246IDEyMy4uLjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMTIwIiBjbGFzcz0iYmFzZSI+Q29uZGl0aW9uOiBGYWlyPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNDUiIGNsYXNzPSJiYXNlIj5BZ2U6IEVzdGFibGlzaGVkPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIxNzAiIGNsYXNzPSJiYXNlIj5GZWF0dXJlczogQmFsY29ueTwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMjIwIiBjbGFzcz0iYmFzZSI+VmFsdWU6ICQ5MDAwMDBNPC90ZXh0Pjx0ZXh0IHg9IjEwIiB5PSIyNDUiIGNsYXNzPSJiYXNlIj5NYXggTFRWOiA4MCU8L3RleHQ+PHRleHQgeD0iMTAiIHk9IjI4MCIgY2xhc3M9ImJhc2UiIHN0eWxlPSJmb250LXNpemU6IDEycHg7Ij5BZGRyZXNzOiAxMjMgVVNEWCBUZXN0IEF2ZSwgVC4uLjwvdGV4dD48dGV4dCB4PSIxMCIgeT0iMzA1IiBjbGFzcz0iYmFzZSI+VHlwZTogUmVzaWRlbnRpYWw8L3RleHQ+PC9zdmc+' | base64 -d > property.svg
```
