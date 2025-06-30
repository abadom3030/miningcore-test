# miningcore-test

// Frontend React básico para Miningcore // Consome API pública do Miningcore e exibe infos da pool // Ideal para hospedagem via Docker

import React, { useEffect, useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Loader } from "lucide-react";

const API_BASE = "http://localhost:4000/api/pools"; // ajuste se necessário const POOLS = ["bitcoin", "bitcoincash", "peercoin", "briskcoin", "litecoincash", "kawra", "ergon"];

export default function MiningPoolDashboard() { const [data, setData] = useState({}); const [loading, setLoading] = useState(true);

useEffect(() => { const fetchData = async () => { const results = {}; for (const pool of POOLS) { try { const res = await fetch(${API_BASE}/${pool}/performance); const perf = await res.json();

const res2 = await fetch(`${API_BASE}/${pool}/miners`);
      const miners = await res2.json();

      const res3 = await fetch(`${API_BASE}/${pool}/payments?page=0&pageSize=5`);
      const payouts = await res3.json();

      results[pool] = {
        hashrate: perf.pool.hashrate,
        miners: miners.length,
        payouts,
      };
    } catch (err) {
      results[pool] = { error: true };
    }
  }
  setData(results);
  setLoading(false);
};

fetchData();

}, []);

if (loading) { return ( <div className="flex justify-center items-center h-screen"> <Loader className="animate-spin w-12 h-12 text-blue-500" /> </div> ); }

return ( <div className="p-6 grid md:grid-cols-2 lg:grid-cols-3 gap-4"> {POOLS.map((pool) => { const info = data[pool]; if (!info || info.error) return null; return ( <Card key={pool} className="shadow-xl"> <CardContent className="p-4"> <h2 className="text-xl font-bold capitalize">{pool}</h2> <p>Miners: {info.miners}</p> <p>Hashrate: {(info.hashrate / 1e12).toFixed(2)} TH/s</p> <h3 className="mt-2 font-semibold">Últimos pagamentos:</h3> <ul className="text-sm mt-1 space-y-1"> {info.payouts?.map((p, i) => ( <li key={i} className="text-gray-700"> {new Date(p.created).toLocaleString()} - {p.amount} {p.currency} </li> )) || <li>Nenhum payout</li>} </ul> </CardContent> </Card> ); })} </div> ); }

