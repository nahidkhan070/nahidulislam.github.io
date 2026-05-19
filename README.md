import React, { useState, useEffect } from 'react';
import Papa from 'papaparse';
import { BarChart3, ArrowUpRight, DollarSign, Layers, FileText } from 'lucide-react';

export default function Dashboard() {
  const [activeTab, setActiveTab] = useState('summary');
  const [summaryData, setSummaryData] = useState([]);
  const [deliveries, setDeliveries] = useState([]);
  const [loading, setLoading] = useState(false);

  // Mock function simulating loading your uploaded CSV data
  useEffect(() => {
    setLoading(true);
    // In production, these can be fetched from your repository's public folder
    // e.g., fetch('/data/summary.csv')
    const sampleSummaryCSV = `SL,Buyer,Previous Balance,Delivery-2026,Adjust-2026,Balance,Mktg\n1,A&A FASHION,309.15,0,0,309.15,MRZ\n2,AWSUM TEX,0,170.75,0,170.75,HH\n3,ALAMIA FASHION,112.5,0,0,112.5,ZR`;
    
    Papa.parse(sampleSummaryCSV, {
      header: true,
      dynamicTyping: true,
      skipEmptyLines: true,
      complete: (results) => {
        setSummaryData(results.data);
        setLoading(false);
      }
    });
  }, []);

  // Calculate quick metrics from the data
  const totalOutstanding = summaryData.reduce((acc, curr) => acc + (curr.Balance || 0), 0);
  const activeBuyers = new Set(summaryData.map(item => item.Buyer)).size;

  return (
    <div className="min-h-screen bg-slate-950 text-slate-100 font-sans p-6">
      {/* Top Header */}
      <header className="mb-8 flex justify-between items-center border-b border-slate-800 pb-5">
        <div>
          <h1 className="text-2xl font-bold bg-gradient-to-r from-emerald-400 to-cyan-400 bg-clip-text text-transparent">
            Yarn Sample Management Portal
          </h1>
          <p className="text-sm text-slate-400 mt-1">Operational Statement & Marketing Analytics System</p>
        </div>
        <div className="flex gap-2 bg-slate-900 p-1 rounded-lg border border-slate-800">
          <button 
            onClick={() => setActiveTab('summary')}
            className={`px-4 py-2 rounded-md text-sm font-medium transition-all ${activeTab === 'summary' ? 'bg-emerald-500 text-slate-950 shadow-lg shadow-emerald-500/20' : 'text-slate-400 hover:text-slate-200'}`}
          >
            Pending Summary
          </button>
          <button 
            onClick={() => setActiveTab('deliveries')}
            className={`px-4 py-2 rounded-md text-sm font-medium transition-all ${activeTab === 'deliveries' ? 'bg-emerald-500 text-slate-950 shadow-lg shadow-emerald-500/20' : 'text-slate-400 hover:text-slate-200'}`}
          >
            Live Deliveries
          </button>
        </div>
      </header>

      {/* KPI Cards */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
        <div className="bg-slate-900/50 backdrop-blur-md border border-slate-800 p-6 rounded-xl relative overflow-hidden group">
          <div className="absolute top-0 right-0 p-4 opacity-10 group-hover:opacity-20 transition-opacity">
            <DollarSign size={80} className="text-emerald-400" />
          </div>
          <p className="text-sm text-slate-400 uppercase tracking-wider font-semibold">Total Outstanding Balance</p>
          <h3 className="text-3xl font-bold text-slate-100 mt-2">${totalOutstanding.toFixed(2)}</h3>
          <span className="flex items-center text-xs text-emerald-400 mt-2 gap-1">
            <ArrowUpRight size={14} /> Global metrics calculated live
          </span>
        </div>

        <div className="bg-slate-900/50 backdrop-blur-md border border-slate-800 p-6 rounded-xl relative overflow-hidden group">
          <div className="absolute top-0 right-0 p-4 opacity-10 group-hover:opacity-20 transition-opacity">
            <Layers size={80} className="text-cyan-400" />
          </div>
          <p className="text-sm text-slate-400 uppercase tracking-wider font-semibold">Active Accounts</p>
          <h3 className="text-3xl font-bold text-slate-100 mt-2">{activeBuyers}</h3>
          <span className="flex items-center text-xs text-cyan-400 mt-2 gap-1">
            <FileText size={14} /> Structured by marketing team
          </span>
        </div>
      </div>

      {/* Data Presentation Area */}
      <div className="bg-slate-900/30 border border-slate-800 rounded-xl overflow-hidden backdrop-blur-md">
        {loading ? (
          <div className="p-12 text-center text-slate-400 animate-pulse">Processing transactional data logs...</div>
        ) : activeTab === 'summary' ? (
          <div className="overflow-x-auto">
            <table className="w-full text-left border-collapse">
              <thead>
                <tr className="bg-slate-900/80 border-b border-slate-800 text-xs font-semibold uppercase tracking-wider text-slate-400">
                  <th className="p-4">SL</th>
                  <th className="p-4">Buyer Name</th>
                  <th className="p-4 text-right">Previous Balance</th>
                  <th className="p-4 text-right">Delivery (2026)</th>
                  <th className="p-4 text-right">Adjustments</th>
                  <th className="p-4 text-right text-emerald-400">Current Balance</th>
                  <th className="p-4 text-center">Marketing</th>
                </tr>
              </thead>
              <tbody className="divide-y divide-slate-800/60 text-sm">
                {summaryData.map((row, index) => (
                  <tr key={index} className="hover:bg-slate-800/30 transition-colors">
                    <td className="p-4 text-slate-500 font-mono">{row.SL || index + 1}</td>
                    <td className="p-4 font-medium text-slate-200">{row.Buyer}</td>
                    <td className="p-4 text-right font-mono text-slate-400">${row['Previous\nBalance'] || row.PreviousBalance || 0}</td>
                    <td className="p-4 text-right font-mono text-cyan-400">${row['Delivery-2026'] || 0}</td>
                    <td className="p-4 text-right font-mono text-amber-400">${row['Adjust-2026'] || 0}</td>
                    <td className="p-4 text-right font-mono font-semibold text-emerald-400">${row.Balance || 0}</td>
                    <td className="p-4 text-center">
                      <span className="px-2.5 py-1 text-xs rounded-full bg-slate-800 text-slate-300 font-mono border border-slate-700">
                        {row.Mktg}
                      </span>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        ) : (
          <div className="p-12 text-center text-slate-500">
            Delivery transactions and lookup modules will render here.
          </div>
        )}
      </div>
    </div>
  );
}
