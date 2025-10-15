# token-staking-dapp
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

contract Staking {
    IERC20 public token;
    mapping(address => uint256) public balance;
    mapping(address => uint256) public lastStakeTime;

    constructor(IERC20 _token) {
        token = _token;
    }

    function stake(uint256 amount) external {
        require(amount > 0, "zero amount");
        token.transferFrom(msg.sender, address(this), amount);
        balance[msg.sender] += amount;
        lastStakeTime[msg.sender] = block.timestamp;
    }

    function unstake() external {
        uint256 staked = balance[msg.sender];
        require(staked > 0, "nothing staked");
        uint256 reward = (block.timestamp - lastStakeTime[msg.sender]) / 60; // 1 token/minute
        balance[msg.sender] = 0;
        token.transfer(msg.sender, staked + reward);
    }
}
