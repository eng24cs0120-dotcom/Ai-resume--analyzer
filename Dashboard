import React, { useState, useEffect } from "react";
import { ResumeAnalysis } from "@/entities/ResumeAnalysis";
import { User } from "@/entities/User";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Link } from "react-router-dom";
import { createPageUrl } from "@/utils";
import { FileText, TrendingUp, Award, Target, Plus, ExternalLink } from "lucide-react";
import { format } from "date-fns";
import { motion } from "framer-motion";

import StatsCard from "../components/dashboard/StatsCard";
import RecentAnalyses from "../components/dashboard/RecentAnalyses";
import QuickUpload from "../components/dashboard/QuickUpload";

export default function Dashboard() {
  const [analyses, setAnalyses] = useState([]);
  const [isLoading, setIsLoading] = useState(true);
  const [user, setUser] = useState(null);

  useEffect(() => {
    loadData();
  }, []);

  const loadData = async () => {
    setIsLoading(true);
    try {
      const currentUser = await User.me();
      setUser(currentUser);
      const data = await ResumeAnalysis.filter(
        { created_by: currentUser.email },
        "-created_date",
        50
      );
      setAnalyses(data);
    } catch (error) {
      console.error("Error loading data:", error);
    }
    setIsLoading(false);
  };

  const avgScore = analyses.length > 0
    ? analyses.reduce((sum, a) => sum + (a.overall_score || 0), 0) / analyses.length
    : 0;

  const topScore = analyses.length > 0
    ? Math.max(...analyses.map(a => a.overall_score || 0))
    : 0;

  return (
    <div className="min-h-screen p-4 md:p-8">
      <div className="max-w-7xl mx-auto space-y-8">
        {/* Header */}
        <motion.div
          initial={{ opacity: 0, y: -20 }}
          animate={{ opacity: 1, y: 0 }}
          className="flex flex-col md:flex-row justify-between items-start md:items-center gap-4"
        >
          <div>
            <h1 className="text-3xl md:text-4xl font-bold text-slate-900 tracking-tight">
              Welcome back{user ? `, ${user.full_name?.split(' ')[0]}` : ''}
            </h1>
            <p className="text-slate-600 mt-2 text-lg">
              Ready to optimize your next resume?
            </p>
          </div>
          <Link to={createPageUrl("Analyzer")}>
            <Button className="bg-gradient-to-r from-indigo-600 to-purple-600 hover:from-indigo-700 hover:to-purple-700 shadow-lg hover:shadow-xl transition-all duration-300 text-white">
              <Plus className="w-5 h-5 mr-2" />
              Analyze New Resume
            </Button>
          </Link>
        </motion.div>

        {/* Stats Cards */}
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
          <StatsCard
            title="Total Analyses"
            value={analyses.length}
            icon={FileText}
            color="indigo"
            isLoading={isLoading}
          />
          <StatsCard
            title="Average Score"
            value={`${avgScore.toFixed(0)}%`}
            icon={TrendingUp}
            color="purple"
            isLoading={isLoading}
          />
          <StatsCard
            title="Top Score"
            value={`${topScore.toFixed(0)}%`}
            icon={Award}
            color="emerald"
            isLoading={isLoading}
          />
          <StatsCard
            title="This Month"
            value={analyses.filter(a => {
              const created = new Date(a.created_date);
              const now = new Date();
              return created.getMonth() === now.getMonth() && created.getFullYear() === now.getFullYear();
            }).length}
            icon={Target}
            color="amber"
            isLoading={isLoading}
          />
        </div>

        {/* Quick Upload */}
        <QuickUpload onAnalysisComplete={loadData} />

        {/* Recent Analyses */}
        <RecentAnalyses analyses={analyses} isLoading={isLoading} />
      </div>
    </div>
  );
}
