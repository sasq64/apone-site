---
layout: default
title: Examples
---

## Core
{% highlight c++ %}
File root { "somedir" };
for(const auto &f : root.listFiles()) {
    if(path_extension(f.getName()) == "csv") {
        for(const auto &line : f.getLines()) {
            auto parts = split(line, ";");
            if(parts.size() >= 3 && stol(parts[2]) >= 2000)
                print_fmt("[%s]\n", parts);
        }
    }
}
{% endhighlight %}

## Database
{% highlight c++ %}
struct User {
    User(uint64_t id, string name) : id(id), name(name) {}
    uint64_t id;
    string name;
};

Database db { "users.db" };
db.exec("CREATE TABLE IF NOT EXISTS user (name TEXT, id INT)");

string userName = "james";
uint64_t id = 123;

db.exec("INSERT INTO user (name, id) VALUES (?, ?)", name, id);

vector<User> users;
auto q = db.query<uint64_t, string>("SELECT id,name FROM user");
while(q.step()) {
    users.push_back(q.get<User>());
}
{% endhighlight %}

## Graphics and Audio

{% highlight c++ %}
screen.open();

// Setup tween to move circle to middle of screen and then blend it to a lighter color
static Color color = 0x880000cc;
static float xpos = -50.0f;
Tween::make().from(xpos, screen.width()/2).seconds(2.0).onComplete([&]() {
    Tween::make().from(color, 0xffee7777).seconds(0.8);
};

// Load a music file and play it
chipmachine::ModPlugin plugin;
auto player = plugin.fromFile("data/stardust.mod");
AudioPlayer aPlayer([=](int16_t *target, int len) mutable {
    player->getSamples(target, len);
});

// Start the render loop
screen.render_loop([=](uint32_t delta) mutable {
    screen.clear();
    screen.circle(xpos, screen.height()/2, 100, color);
    screen.flip();
});
{% endhighlight %}

## LUA
{% highlight c++ %}
LuaInterpreter lua;

// Register a function callable from lua, with specific arguments
lua.registerFunction<int, string, int, float>("testFunc", [=](string s, int x, float f) -> int {
    LOGD("Got '%s' and %f", s, f);
    return x+10;
});

// Load a lua function into the interpreter
const string luaCode = R"(
function test (a, b)
    x = testFunc('hello', 3, 2.0)
    return 2*x
end
)";
lua.load(luaCode);

// Try calling the lua function, which in turn calls the C++ function
int res = lua.call<double>("test", 4, 2);

// res should be 26 here
{% endhighlight %}

## Web
{% highlight c++ %}
WebRPC rpc("http://something-funny-123.appspot.com/");

JSon json;
json.add("uid", to_string(trackid));
json.add("name", name);
json.add("version", API_VERSION);
auto sl = json.add_array("songs");
for(const SongInfo &info : songs) {
    sl.add(info.path);
}
rpc.post("get_song_url", json.to_string(), [=](const string &result) {
    JSon json = JSon::parse(result);
    for(auto pl : json["urls"]) {
        list.emplace_back(pl.to_string());
    }
});
{% endhighlight %}

## Telnet Server

{% highlight c++ %}
TelnetServer telnet { 12345 };
telnet.onConnect([](TelnetServer::Session &session) {
    session.write("name:");
    auto name = session.getLine();
    session.write(format("goodbye %s\r\n", name));
    session.close();
});
telnet.run();
{% endhighlight %}
