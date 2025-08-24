# ai-agent-hackthon
import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Bell, Instagram, Youtube, Linkedin } from "lucide-react";
import { motion } from "framer-motion";

export default function InfluencerMonitorUI() {
  const [youtubePosts, setYoutubePosts] = useState([]);
  const [instagramPosts, setInstagramPosts] = useState([]);
  const [linkedinPosts, setLinkedinPosts] = useState([]);
  const [trendBrief, setTrendBrief] = useState(null);
  const [newHandle, setNewHandle] = useState("");

  // Fetch posts from your n8n workflow (replace URL with your n8n webhook)
  useEffect(() => {
    async function fetchData() {
      try {
        const yt = await fetch("http://localhost:5678/webhook/youtube").then(res => res.json());
        const ig = await fetch("http://localhost:5678/webhook/instagram").then(res => res.json());
        const li = await fetch("http://localhost:5678/webhook/linkedin").then(res => res.json());
        const tb = await fetch("http://localhost:5678/webhook/trendbrief").then(res => res.json());
        setYoutubePosts(yt || []);
        setInstagramPosts(ig || []);
        setLinkedinPosts(li || []);
        setTrendBrief(tb);
      } catch (e) {
        console.error("Error fetching data from n8n:", e);
      }
    }
    fetchData();
  }, []);

  const handleAddInfluencer = async () => {
    if (!newHandle) return;
    try {
      await fetch("http://localhost:5678/webhook/add-handle", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ handle: newHandle })
      });
      setNewHandle("");
      alert("Influencer added successfully via n8n!");
    } catch (e) {
      console.error("Error adding influencer:", e);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 via-black to-gray-800 p-6 text-white">
      <header className="flex justify-between items-center mb-8">
        <motion.h1 
          initial={{ opacity: 0, y: -20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.6 }}
          className="text-3xl font-extrabold bg-gradient-to-r from-pink-500 via-purple-500 to-blue-500 bg-clip-text text-transparent drop-shadow-lg"
        >
          Influencer Monitoring Dashboard
        </motion.h1>
        <motion.div whileHover={{ scale: 1.1 }}>
          <Button onClick={handleAddInfluencer} className="rounded-2xl shadow-[0_0_15px_#ff00ff] bg-gradient-to-r from-pink-500 to-purple-600 text-white border border-purple-400">
            Add Influencer
          </Button>
        </motion.div>
      </header>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <motion.div
          initial={{ opacity: 0, x: -30 }}
          animate={{ opacity: 1, x: 0 }}
          transition={{ duration: 0.7 }}
          className="col-span-2"
        >
          <Card className="bg-gray-900/70 backdrop-blur-md shadow-[0_0_20px_#00f] rounded-2xl">
            <CardContent className="p-4">
              <Tabs defaultValue="youtube">
                <TabsList className="mb-4 bg-gray-800 rounded-xl">
                  <TabsTrigger value="youtube" className="flex items-center gap-2 text-white">
                    <Youtube className="h-4 w-4 text-red-500" /> YouTube
                  </TabsTrigger>
                  <TabsTrigger value="instagram" className="flex items-center gap-2 text-white">
                    <Instagram className="h-4 w-4 text-pink-500" /> Instagram
                  </TabsTrigger>
                  <TabsTrigger value="linkedin" className="flex items-center gap-2 text-white">
                    <Linkedin className="h-4 w-4 text-blue-400" /> LinkedIn
                  </TabsTrigger>
                </TabsList>

                <TabsContent value="youtube">
                  {youtubePosts.length > 0 ? (
                    youtubePosts.map((post, i) => (
                      <Card key={i} className="p-3 bg-gray-800 rounded-xl shadow-[0_0_10px_#f00] mb-2">
                        <h3 className="font-semibold text-pink-400">{post.author}</h3>
                        <p className="text-sm text-gray-300">{post.title}</p>
                        <span className="text-xs text-gray-400">{post.publishedAt}</span>
                      </Card>
                    ))
                  ) : (
                    <p className="text-gray-400 italic">No YouTube posts yet...</p>
                  )}
                </TabsContent>

                <TabsContent value="instagram">
                  {instagramPosts.length > 0 ? (
                    instagramPosts.map((post, i) => (
                      <Card key={i} className="p-3 bg-gray-800 rounded-xl shadow-[0_0_10px_#f0f] mb-2">
                        <h3 className="font-semibold text-pink-400">{post.author}</h3>
                        <p className="text-sm text-gray-300">{post.content}</p>
                        <span className="text-xs text-gray-400">{post.publishedAt}</span>
                      </Card>
                    ))
                  ) : (
                    <p className="text-gray-400 italic">No Instagram posts yet...</p>
                  )}
                </TabsContent>

                <TabsContent value="linkedin">
                  {linkedinPosts.length > 0 ? (
                    linkedinPosts.map((post, i) => (
                      <Card key={i} className="p-3 bg-gray-800 rounded-xl shadow-[0_0_10px_#0ff] mb-2">
                        <h3 className="font-semibold text-blue-400">{post.author}</h3>
                        <p className="text-sm text-gray-300">{post.content}</p>
                        <span className="text-xs text-gray-400">{post.publishedAt}</span>
                      </Card>
                    ))
                  ) : (
                    <p className="text-gray-400 italic">No LinkedIn posts yet...</p>
                  )}
                </TabsContent>
              </Tabs>
            </CardContent>
          </Card>
        </motion.div>

        <motion.div
          initial={{ opacity: 0, x: 30 }}
          animate={{ opacity: 1, x: 0 }}
          transition={{ duration: 0.7 }}
        >
          <Card className="bg-gray-900/70 backdrop-blur-md shadow-[0_0_20px_#ff0] rounded-2xl">
            <CardContent className="p-4 space-y-4">
              <h2 className="text-lg font-bold flex items-center gap-2 text-yellow-300">
                <Bell className="h-5 w-5 text-yellow-400 animate-bounce" /> 48h Trend Brief
              </h2>
              {trendBrief ? (
                <motion.div
                  initial={{ scale: 0.9, opacity: 0 }}
                  animate={{ scale: 1, opacity: 1 }}
                  transition={{ duration: 0.5 }}
                  className="bg-gray-800 p-3 rounded-xl text-sm text-gray-200 shadow-inner shadow-purple-500/40"
                >
                  <p><strong className="text-pink-400">Top Trend:</strong> {trendBrief.topTrend}</p>
                  <p><strong className="text-blue-400">Notable Insight:</strong> {trendBrief.insight}</p>
                  <p><strong className="text-green-400">Next Step:</strong> {trendBrief.nextStep}</p>
                </motion.div>
              ) : (
                <p className="text-gray-400 italic">Waiting for trend brief...</p>
              )}
              <Button className="w-full rounded-xl bg-gradient-to-r from-yellow-400 to-orange-500 text-black font-bold shadow-[0_0_12px_#ff0]">
                Download Report
              </Button>
            </CardContent>
          </Card>
        </motion.div>
      </div>

      <motion.div 
        initial={{ opacity: 0, y: 30 }}
        animate={{ opacity: 1, y: 0 }}
        transition={{ duration: 0.8 }}
        className="mt-8"
      >
        <h2 className="text-lg font-bold mb-2 text-purple-300">Add New Influencer</h2>
        <div className="flex gap-2">
          <Input value={newHandle} onChange={e => setNewHandle(e.target.value)} placeholder="Enter influencer handle" className="bg-gray-800 text-white border-purple-500 focus:ring-pink-400" />
          <Button onClick={handleAddInfluencer} className="rounded-xl bg-gradient-to-r from-pink-500 to-purple-600 text-white shadow-[0_0_10px_#f0f]">Save</Button>
        </div>
      </motion.div>
    </div>
