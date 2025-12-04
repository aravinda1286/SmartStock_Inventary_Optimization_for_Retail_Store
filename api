import { useState, useRef, useEffect } from "react";
import { Send, Key, Save, Bot, User, AlertCircle } from "lucide-react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { toast } from "sonner";

type Message = {
  role: "user" | "assistant";
  content: string;
};

const Index = () => {
  const [apiKey, setApiKey] = useState("");
  const [savedApiKey, setSavedApiKey] = useState("");
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState("");
  const [isLoading, setIsLoading] = useState(false);
  const messagesEndRef = useRef<HTMLDivElement>(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const handleSaveApiKey = () => {
    if (!apiKey.trim()) {
      toast.error("Please enter an API key");
      return;
    }
    setSavedApiKey(apiKey);
    toast.success("API key saved successfully!");
  };

  const sendMessage = async () => {
    if (!input.trim()) return;
    if (!savedApiKey) {
      toast.error("Please save your API key first");
      return;
    }

    const userMessage: Message = { role: "user", content: input };
    setMessages((prev) => [...prev, userMessage]);
    setInput("");
    setIsLoading(true);

    try {
      const response = await fetch("https://api.openai.com/v1/chat/completions", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${savedApiKey}`,
        },
        body: JSON.stringify({
          model: "gpt-3.5-turbo",
          messages: [...messages, userMessage].map((m) => ({
            role: m.role,
            content: m.content,
          })),
        }),
      });

      if (!response.ok) {
        if (response.status === 401) {
          throw new Error("Invalid API key. Please check your key and try again.");
        }
        throw new Error(`API error: ${response.status}`);
      }

      const data = await response.json();
      const assistantMessage: Message = {
        role: "assistant",
        content: data.choices[0].message.content,
      };
      setMessages((prev) => [...prev, assistantMessage]);
    } catch (error) {
      const errorMessage = error instanceof Error ? error.message : "Network error occurred";
      toast.error(errorMessage);
      setMessages((prev) => prev.slice(0, -1));
    } finally {
      setIsLoading(false);
    }
  };

  const handleKeyPress = (e: React.KeyboardEvent) => {
    if (e.key === "Enter" && !e.shiftKey) {
      e.preventDefault();
      sendMessage();
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-900 via-slate-800 to-slate-900 flex flex-col">
      {/* Header with API Key Input */}
      <header className="border-b border-slate-700/50 bg-slate-900/80 backdrop-blur-sm p-4">
        <div className="max-w-4xl mx-auto flex flex-col sm:flex-row gap-3 items-center">
          <div className="flex items-center gap-2 text-cyan-400">
            <Key className="w-5 h-5" />
            <span className="text-sm font-medium">Enter API Key</span>
          </div>
          <div className="flex-1 flex gap-2 w-full sm:w-auto">
            <Input
              type="password"
              placeholder="sk-..."
              value={apiKey}
              onChange={(e) => setApiKey(e.target.value)}
              className="flex-1 bg-slate-800/50 border-slate-700 text-slate-100 placeholder:text-slate-500 focus:border-cyan-500 focus:ring-cyan-500/20"
            />
            <Button
              onClick={handleSaveApiKey}
              className="bg-cyan-600 hover:bg-cyan-500 text-white gap-2"
            >
              <Save className="w-4 h-4" />
              Save
            </Button>
          </div>
          {savedApiKey && (
            <span className="text-xs text-emerald-400 flex items-center gap-1">
              ✓ Key saved
            </span>
          )}
        </div>
      </header>

      {/* Chat Area */}
      <main className="flex-1 max-w-4xl mx-auto w-full flex flex-col p-4 overflow-hidden">
        <div className="flex-1 overflow-y-auto space-y-4 pb-4 scrollbar-thin scrollbar-thumb-slate-700 scrollbar-track-transparent">
          {messages.length === 0 && (
            <div className="flex flex-col items-center justify-center h-full text-slate-500 gap-4">
              <Bot className="w-16 h-16 text-cyan-500/50" />
              <p className="text-lg">Start a conversation</p>
              <p className="text-sm text-slate-600">Enter your OpenAI API key above to begin</p>
            </div>
          )}

          {messages.map((message, index) => (
            <div
              key={index}
              className={`flex items-start gap-3 ${
                message.role === "user" ? "flex-row-reverse" : ""
              }`}
            >
              <div
                className={`w-8 h-8 rounded-full flex items-center justify-center flex-shrink-0 ${
                  message.role === "user"
                    ? "bg-cyan-600"
                    : "bg-slate-700"
                }`}
              >
                {message.role === "user" ? (
                  <User className="w-4 h-4 text-white" />
                ) : (
                  <Bot className="w-4 h-4 text-cyan-400" />
                )}
              </div>
              <div
                className={`max-w-[80%] px-4 py-3 rounded-2xl ${
                  message.role === "user"
                    ? "bg-cyan-600 text-white rounded-tr-md"
                    : "bg-slate-700/80 text-slate-100 rounded-tl-md"
                }`}
              >
                <p className="whitespace-pre-wrap text-sm leading-relaxed">
                  {message.content}
                </p>
              </div>
            </div>
          ))}

          {isLoading && (
            <div className="flex items-start gap-3">
              <div className="w-8 h-8 rounded-full bg-slate-700 flex items-center justify-center">
                <Bot className="w-4 h-4 text-cyan-400" />
              </div>
              <div className="bg-slate-700/80 px-4 py-3 rounded-2xl rounded-tl-md">
                <div className="flex items-center gap-2 text-slate-400 text-sm">
                  <span className="animate-pulse">AI is thinking</span>
                  <span className="flex gap-1">
                    <span className="w-1.5 h-1.5 bg-cyan-400 rounded-full animate-bounce" style={{ animationDelay: "0ms" }} />
                    <span className="w-1.5 h-1.5 bg-cyan-400 rounded-full animate-bounce" style={{ animationDelay: "150ms" }} />
                    <span className="w-1.5 h-1.5 bg-cyan-400 rounded-full animate-bounce" style={{ animationDelay: "300ms" }} />
                  </span>
                </div>
              </div>
            </div>
          )}

          <div ref={messagesEndRef} />
        </div>

        {/* Input Area */}
        <div className="border-t border-slate-700/50 pt-4">
          <div className="flex gap-3">
            <Input
              placeholder="Type your message..."
              value={input}
              onChange={(e) => setInput(e.target.value)}
              onKeyPress={handleKeyPress}
              disabled={isLoading || !savedApiKey}
              className="flex-1 bg-slate-800/50 border-slate-700 text-slate-100 placeholder:text-slate-500 focus:border-cyan-500 focus:ring-cyan-500/20 py-6"
            />
            <Button
              onClick={sendMessage}
              disabled={isLoading || !input.trim() || !savedApiKey}
              className="bg-cyan-600 hover:bg-cyan-500 disabled:bg-slate-700 text-white px-6"
            >
              <Send className="w-5 h-5" />
            </Button>
          </div>
          {!savedApiKey && (
            <p className="text-xs text-amber-500 mt-2 flex items-center gap-1">
              <AlertCircle className="w-3 h-3" />
              Please save your API key to start chatting
            </p>
          )}
        </div>
      </main>
    </div>
  );
};

export default Index;
