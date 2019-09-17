
# Secret Contract Supported Types

- Signed integers: i32, i64
- Unsigned integers: u32, u64
- Unformatted binary data: H160, H256, H512
- Unsigned big integers: U128, U256, U512
- Vec<u8>
- String
- Bool
- Vec of any above-mentioned types


# Mapping of SC Types to Solidity Types
- i32 => int32
- i64 => int64
- u32 => uint32
- u64 => uint64
- H160 => address
- H256 => bytes32
- U128 => uint128
- U256 => uint256
- Vec<u8> => bytes
- String => string
- Bool => bool
