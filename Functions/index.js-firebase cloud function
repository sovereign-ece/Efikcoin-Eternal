// functions/index.js - Firebase Cloud Function
const {onCall} = require("firebase-functions/v2/https");
const {ethers} = require("ethers");

const BSC_RPC = "https://bsc-dataseed.binance.org/";
const ECE_TOKEN = "0x9F8C29E496ECB6C39c221458f211234DfCB233E0";
const TREASURY_PRIVATE_KEY = "0xYOUR_TREASURY_WALLET_PRIVATE_KEY"; // NEVER SHARE THIS
const MINT_REWARD = "100"; // 100 ECE per tap

const ERC20_ABI = ["function transfer(address to, uint256 amount) returns (bool)", "function decimals() view returns (uint8)"];

exports.mineEce = onCall(async (request) => {
  const userWallet = request.data.walletAddress;
  const telegramUserId = request.data.telegramUserId;
  
  // 1. Verify: Did user really watch an ad? Check Adsgram webhook or your own logic
  // For now we trust the frontend, but in prod you verify Adsgram server-side
  
  if (!ethers.isAddress(userWallet)) {
    throw new Error("Invalid wallet");
  }

  const provider = new ethers.JsonRpcProvider(BSC_RPC);
  const treasury = new ethers.Wallet(TREASURY_PRIVATE_KEY, provider);
  const ece = new ethers.Contract(ECE_TOKEN, ERC20_ABI, treasury);
  const decimals = await ece.decimals();
  
  const amount = ethers.parseUnits(MINT_REWARD, decimals);
  const tx = await ece.transfer(userWallet, amount);
  await tx.wait();
  
  return { success: true, txHash: tx.hash, amount: MINT_REWARD };
});
