# MinimalERC721

A gas-optimized ERC721 implementation that reduces transaction costs for NFT projects where ownership transfers are infrequent. This contract is designed specifically for use cases like information tracking and archival systems where NFTs are typically owned by a single default account.

## Overview

MinimalERC721 is based on OpenZeppelin's ERC721 implementation but includes several key optimizations to minimize gas costs:

- **Default Owner Pattern**: Tokens are owned by a default owner address by default, avoiding the need to store ownership data for most tokens
- **Optimized Storage**: Only stores ownership data when tokens are transferred away from the default owner
- **Burn Protection**: Prevents token burning to maintain data integrity
- **Reentrancy Protection**: Built-in reentrancy guard for secure transfers

## Key Features

### Gas Optimizations

1. **Default Owner System**: 
   - Tokens minted to the default owner don't require storage of ownership mapping
   - Significant gas savings when minting large quantities of NFTs to a single address
   - Ownership data is only stored when tokens are transferred to non-default addresses

2. **Efficient Minting**:
   - Reduced storage operations during mint when recipient is the default owner
   - Balance tracking optimized for default owner scenario

3. **Smart Storage Management**:
   - Automatically deletes ownership records when tokens are transferred back to default owner
   - Minimizes blockchain storage usage and associated costs

### Security Features

- **Reentrancy Protection**: Built-in guard prevents reentrancy attacks during transfers
- **Burn Prevention**: Tokens cannot be burned, ensuring data permanence
- **Standard Compliance**: Fully compliant with ERC721 and ERC721Metadata standards

## Usage

### Basic Implementation

Replace your standard ERC721 import with MinimalERC721:

```solidity
// Instead of:
// import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

// Use:
import "./minimalERC721.sol";

contract MyNFT is ERC721 {
    address public defaultOwner;
    
    constructor(string memory name, string memory symbol, address _defaultOwner) 
        ERC721(name, symbol) 
    {
        defaultOwner = _defaultOwner;
        _setDefaultOwner(_defaultOwner);
    }
    
    function mint(address to, uint256 tokenId) public {
        _mint(to, tokenId);
    }
}
```

### Setting Default Owner

```solidity
function setDefaultOwner(address newDefaultOwner) public onlyOwner {
    _setDefaultOwner(newDefaultOwner);
}
```

## API Reference

### Constructor

```solidity
constructor(string memory name_, string memory symbol_)
```

### Key Functions

#### `_setDefaultOwner(address defaultOwner)`
Sets the default owner address. Transfers all balance from the previous default owner to the new one.

#### `ownerOf(uint256 tokenId)`
Returns the owner of a token. If no specific owner is stored, returns the default owner.

#### `_mint(address to, uint256 tokenId)`
Mints a new token. If minting to the default owner, saves gas by not storing ownership data.

#### `_burn(uint256 tokenId)`
**Disabled** - This function will revert to prevent token burning.

## Use Cases

MinimalERC721 is ideal for:

- **Information Archival Systems**: Where most tokens remain with a primary archival address
- **Document Tracking**: Legal or academic document NFTs that rarely change hands
- **Certificate Systems**: Educational or professional certificates typically held by institutions
- **Data Provenance**: Tracking data lineage where ownership transfers are minimal
- **Content Archives**: News articles, social media posts, or other content preservation

## Requirements

- Solidity ^0.8.24
- OpenZeppelin Contracts v5.4.0

## License

MIT License - see LICENSE file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Security Considerations

- This contract has been optimized for specific use cases where ownership transfers are rare
- Always test thoroughly in your specific environment before production deployment
- Consider professional security audits for high-value deployments
- The reentrancy guard provides basic protection but additional security measures may be needed based on your implementation

## Support

For questions or support, please contact [support@trutharchive.ai](mailto:support@trutharchive.ai) or open an issue on GitHub.