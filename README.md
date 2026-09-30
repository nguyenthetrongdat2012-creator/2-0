--// DATBEO SCRIPT - FULL FIXED (78 BÀI + AUTO CÂU FIX)
local Players    = game:GetService("Players")
local CoreGui    = game:GetService("CoreGui")
local Tween      = game:GetService("TweenService")
local VIM        = game:GetService("VirtualInputManager")
local Sound      = game:GetService("SoundService")
local UIS        = game:GetService("UserInputService")
local P          = Players.LocalPlayer

if CoreGui:FindFirstChild("DatBeoGui") then CoreGui.DatBeoGui:Destroy() end

local S = Instance.new("ScreenGui")
S.Name = "DatBeoGui"
S.ResetOnSpawn = false
S.Parent = CoreGui

local C = {
    bg = Color3.fromRGB(15,15,20), bg2 = Color3.fromRGB(25,25,35),
    gold = Color3.fromRGB(255,200,60), cyan = Color3.fromRGB(0,220,255),
    grn = Color3.fromRGB(0,255,130), red = Color3.fromRGB(255,60,80),
    purple = Color3.fromRGB(180, 100, 255),
    dim = Color3.fromRGB(130,130,145),
}

-- ================= STEALTH =================
local STEALTH = { Enabled = true, RandomDelay = true, AntiAFK = true }

local function rndDelay(base, variance)
    if not STEALTH.Enabled or not STEALTH.RandomDelay then return task.wait(base) end
    local v = variance or base * 0.4
    local final = base + (math.random() * 2 - 1) * v
    if final < 0.05 then final = 0.05 end
    return task.wait(final)
end

if STEALTH.AntiAFK then
    task.spawn(function()
        while task.wait(45 + math.random() * 30) do
            pcall(function()
                VIM:SendKeyEvent(true, Enum.KeyCode.LeftShift, false, game)
                task.wait(0.05)
                VIM:SendKeyEvent(false, Enum.KeyCode.LeftShift, false, game)
            end)
        end
    end)
end

-- ================= HÀM CHUNG =================
local function corner(p,r) local c=Instance.new("UICorner",p); c.CornerRadius=UDim.new(0,r or 8) end
local function stroke(p,c,t,tr) local s=Instance.new("UIStroke",p); s.Color=c or C.gold; s.Thickness=t or 1.5; s.Transparency=tr or 0.3; return s end
local function grad(p,c1,c2,r) local g=Instance.new("UIGradient",p); g.Color=ColorSequence.new(c1,c2); g.Rotation=r or 90 end

_G.MenuLocked = false

local function drag(f)
    local d,ds,sp
    f.InputBegan:Connect(function(i)
        if _G.MenuLocked then return end
        if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
            d=true; ds=i.Position; sp=f.Position
            i.Changed:Connect(function() if i.UserInputState==Enum.UserInputState.End then d=false end end)
        end
    end)
    f.InputChanged:Connect(function(i)
        if _G.MenuLocked then return end
        if d and (i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch) then
            local x=i.Position-ds
            f.Position=UDim2.new(sp.X.Scale,sp.X.Offset+x.X,sp.Y.Scale,sp.Y.Offset+x.Y)
        end
    end)
end

local function btn(parent,size,pos,text)
    local b=Instance.new("TextButton",parent)
    b.Size=size; b.Position=pos
    b.BackgroundColor3=Color3.fromRGB(30,30,40)
    b.BackgroundTransparency = 0.15
    b.TextColor3=Color3.fromRGB(220,220,230)
    b.TextSize=11; b.Font=Enum.Font.GothamBold; b.Text=text
    b.BorderSizePixel=0; b.AutoButtonColor=false
    b.ZIndex = 10
    b.TextStrokeTransparency = 0
    b.TextStrokeColor3 = Color3.fromRGB(0,0,0)
    corner(b,6)
    b.MouseEnter:Connect(function() Tween:Create(b,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(50,50,65)}):Play() end)
    b.MouseLeave:Connect(function() Tween:Create(b,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(30,30,40)}):Play() end)
    return b
end

local Islands = {
    [1] = {name = "🏝️ Đảo 1", pos = Vector3.new(-41.90, 11.09, 303740)},
    [2] = {name = "🌴 Đảo 2", pos = Vector3.new(-1166.36, 10.68, -66.31)},
    [3] = {name = "🏜️ Đảo 3", pos = Vector3.new(-51.02, 10.13, -954.41)},
    [4] = {name = "❄️ Đảo 4", pos = Vector3.new(1095.07, 9.38, -284.33)},
    [5] = {name = "🌋 Đảo 5", pos = Vector3.new(1807.50, 9.17, 1073.44)},
}

-- ================= CLICK FIX =================
local function Click(hold)
    hold = hold or 0.15
    -- Cách 1: mouse1click
    pcall(function() mouse1click() end)
    -- Cách 2: mouse1press/release
    pcall(function()
        mouse1press()
        task.wait(hold)
        mouse1release()
    end)
    -- Cách 3: VIM backup
    pcall(function()
        local cam = workspace.CurrentCamera
        if cam then
            local vp = cam.ViewportSize
            local cx = vp.X / 2
            local cy = vp.Y / 2
            VIM:SendMouseButtonEvent(cx, cy, 0, true, game, 0)
            task.wait(hold)
            VIM:SendMouseButtonEvent(cx, cy, 0, false, game, 0)
        end
    end)
end

local function K(key, delay)
    delay = delay or 0
    pcall(function() keypress(key) keyrelease(key) end)
    pcall(function()
        VIM:SendKeyEvent(true, key, false, game)
        VIM:SendKeyEvent(false, key, false, game)
    end)
    if delay > 0 then rndDelay(delay, delay*0.3) end
end

-- ================= GUI CHÍNH =================
local Main = Instance.new("Frame", S)
Main.Size = UDim2.new(0, 460, 0, 360)
Main.Position = UDim2.new(0.5, -230, 0.5, -180)
Main.BackgroundColor3 = C.bg
Main.BackgroundTransparency = 0.15
Main.BorderSizePixel = 0
Main.Visible = false
corner(Main, 12)
local MS = stroke(Main, C.gold, 1.5, 0.3)
drag(Main)

local BgGrad = Instance.new("UIGradient", Main)
BgGrad.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0,   Color3.fromRGB(45, 20, 60)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(15, 45, 55)),
    ColorSequenceKeypoint.new(1,   Color3.fromRGB(60, 20, 40)),
})
task.spawn(function()
    local rot = 0
    while Main and Main.Parent do
        rot = (rot + 1.5) % 360
        BgGrad.Rotation = rot
        task.wait(0.03)
    end
end)

local Head = Instance.new("Frame", Main)
Head.Size = UDim2.new(1, 0, 0, 32)
Head.BackgroundColor3 = C.bg2
Head.BackgroundTransparency = 0.2
Head.BorderSizePixel = 0
Head.ZIndex = 5
corner(Head, 12)
grad(Head, Color3.fromRGB(45,38,25), Color3.fromRGB(25,25,35))

local Title = Instance.new("TextLabel", Head)
Title.Size = UDim2.new(1, 0, 1, 0)
Title.BackgroundTransparency = 1
Title.TextColor3 = C.gold
Title.TextSize = 13
Title.Font = Enum.Font.GothamBold
Title.Text = "⚡ DATBEO SCRIPT ⚡"
Title.ZIndex = 6
Title.TextStrokeTransparency = 0

local BtnLockMenu = Instance.new("TextButton", Main)
BtnLockMenu.Size = UDim2.new(0, 20, 0, 20)
BtnLockMenu.Position = UDim2.new(1, -74, 0, 6)
BtnLockMenu.BackgroundColor3 = Color3.fromRGB(30, 40, 50)
BtnLockMenu.TextColor3 = C.grn
BtnLockMenu.TextSize = 12
BtnLockMenu.Font = Enum.Font.GothamBold
BtnLockMenu.Text = "🔓"
BtnLockMenu.BorderSizePixel = 0
BtnLockMenu.ZIndex = 10
corner(BtnLockMenu, 4)

local Close = Instance.new("TextButton", Main)
Close.Size = UDim2.new(0, 20, 0, 20)
Close.Position = UDim2.new(1, -26, 0, 6)
Close.BackgroundColor3 = Color3.fromRGB(50,30,30)
Close.TextColor3 = C.red
Close.TextSize = 12
Close.Font = Enum.Font.GothamBold
Close.Text = "✕"
Close.BorderSizePixel = 0
Close.ZIndex = 10
corner(Close, 4)

local Sidebar = Instance.new("Frame", Main)
Sidebar.Size = UDim2.new(0, 80, 1, -45)
Sidebar.Position = UDim2.new(0, 8, 0, 38)
Sidebar.BackgroundTransparency = 1
Sidebar.ZIndex = 5

local function sidebarBtn(pos, text, color)
    local b = Instance.new("TextButton", Sidebar)
    b.Size = UDim2.new(1, 0, 0, 34)
    b.Position = UDim2.new(0, 0, 0, pos)
    b.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    b.BackgroundTransparency = 0.15
    b.TextColor3 = color
    b.TextSize = 10
    b.Font = Enum.Font.GothamBold
    b.Text = text
    b.BorderSizePixel = 0
    b.ZIndex = 10
    b.TextStrokeTransparency = 0
    corner(b, 6)
    return b
end

local BtnTabFarm = sidebarBtn(0, "🌾 FARM", C.grn)
local BtnTabSell = sidebarBtn(38, "💰 SELL", C.dim)
local BtnTabTele = sidebarBtn(76, "🌊 TELE", C.dim)
local BtnTabESP  = sidebarBtn(114, "👁️ ESP", C.dim)
local BtnTabMusic = sidebarBtn(152, "🎵 NHẠC", C.dim)

local ContentX = 96
local ContentW = 356

-- ================= HUD TỌA ĐỘ =================
local CoordHUD = Instance.new("Frame", S)
CoordHUD.Size = UDim2.new(0, 220, 0, 40)
CoordHUD.Position = UDim2.new(0, 15, 0, 60)
CoordHUD.BackgroundColor3 = Color3.fromRGB(20, 25, 35)
CoordHUD.BackgroundTransparency = 0.2
CoordHUD.BorderSizePixel = 0
CoordHUD.ZIndex = 100
corner(CoordHUD, 8)
stroke(CoordHUD, C.cyan, 1.5, 0.3)
drag(CoordHUD)

local CoordText = Instance.new("TextLabel", CoordHUD)
CoordText.Size = UDim2.new(1, -10, 1, 0)
CoordText.Position = UDim2.new(0, 5, 0, 0)
CoordText.BackgroundTransparency = 1
CoordText.TextColor3 = C.grn
CoordText.TextSize = 12
CoordText.Font = Enum.Font.GothamBold
CoordText.Text = "📍 0, 0, 0"
CoordText.ZIndex = 101

task.spawn(function()
    while true do
        task.wait(0.2)
        local char = P.Character
        if char then
            local hrp = char:FindFirstChild("HumanoidRootPart")
            if hrp then
                CoordText.Text = string.format("📍 %.1f, %.1f, %.1f", hrp.Position.X, hrp.Position.Y, hrp.Position.Z)
            end
        end
    end
end)

-- ================= FARM FRAME =================
local FarmFrame = Instance.new("Frame", Main)
FarmFrame.Size = UDim2.new(0, ContentW, 1, -50)
FarmFrame.Position = UDim2.new(0, ContentX, 0, 44)
FarmFrame.BackgroundTransparency = 1
FarmFrame.Visible = true
FarmFrame.ZIndex = 5

local BtnAuto = btn(FarmFrame, UDim2.new(1, 0, 0, 32), UDim2.new(0, 0, 0, 0), "🎣 AUTO CÂU: OFF")
BtnAuto.TextColor3 = C.grn
BtnAuto.TextSize = 12

local BtnSkill = btn(FarmFrame, UDim2.new(0.49, -2, 0, 28), UDim2.new(0, 0, 0, 38), "⚔️ SKILL: OFF")
BtnSkill.TextColor3 = C.grn

local BtnMini = btn(FarmFrame, UDim2.new(0.49, -2, 0, 28), UDim2.new(0.51, 2, 0, 38), "🎮 MINI: OFF")
BtnMini.TextColor3 = C.grn

local BtnLock = btn(FarmFrame, UDim2.new(0.49, -2, 0, 28), UDim2.new(0, 0, 0, 72), "🔒 KHÓA: OFF")
BtnLock.TextColor3 = C.grn

local BtnUnlock = btn(FarmFrame, UDim2.new(0.49, -2, 0, 28), UDim2.new(0.51, 2, 0, 72), "🔓 MỞ KHÓA")
BtnUnlock.TextColor3 = C.gold

local BtnStealth = btn(FarmFrame, UDim2.new(1, 0, 0, 28), UDim2.new(0, 0, 0, 106), "🛡️ STEALTH: ON")
BtnStealth.TextColor3 = C.grn

local BtnTestClick = btn(FarmFrame, UDim2.new(1, 0, 0, 28), UDim2.new(0, 0, 0, 140), "🧪 TEST CLICK")
BtnTestClick.TextColor3 = C.cyan

-- ================= SELL FRAME =================
local SellFrame = Instance.new("Frame", Main)
SellFrame.Size = UDim2.new(0, ContentW, 1, -50)
SellFrame.Position = UDim2.new(0, ContentX, 0, 44)
SellFrame.BackgroundTransparency = 1
SellFrame.Visible = false
SellFrame.ZIndex = 5

local BtnSell = btn(SellFrame, UDim2.new(0.49, -2, 0, 32), UDim2.new(0, 0, 0, 0), "💰 SELL + VỀ")
BtnSell.TextColor3 = C.gold
BtnSell.TextSize = 11

local BtnSellMax = btn(SellFrame, UDim2.new(0.49, -2, 0, 32), UDim2.new(0.51, 2, 0, 0), "🔥 SELL MAX")
BtnSellMax.TextColor3 = C.red
BtnSellMax.TextSize = 11

local BtnMilestone = btn(SellFrame, UDim2.new(1, 0, 0, 30), UDim2.new(0, 0, 0, 38), "🎯 MỐC BÁN: 50 CÁ")
BtnMilestone.TextColor3 = C.cyan

local BtnAutoSell = btn(SellFrame, UDim2.new(1, 0, 0, 30), UDim2.new(0, 0, 0, 74), "💰 AUTO SELL: OFF")
BtnAutoSell.TextColor3 = C.gold

-- ================= TELE FRAME =================
local TeleFrame = Instance.new("Frame", Main)
TeleFrame.Size = UDim2.new(0, ContentW, 1, -50)
TeleFrame.Position = UDim2.new(0, ContentX, 0, 44)
TeleFrame.BackgroundTransparency = 1
TeleFrame.Visible = false
TeleFrame.ZIndex = 5

local XYZLabel = Instance.new("TextLabel", TeleFrame)
XYZLabel.Size = UDim2.new(1, 0, 0, 16)
XYZLabel.Position = UDim2.new(0, 0, 0, 0)
XYZLabel.BackgroundTransparency = 1
XYZLabel.TextColor3 = C.dim
XYZLabel.TextSize = 10
XYZLabel.Font = Enum.Font.GothamBold
XYZLabel.Text = "Nhập tọa độ X / Y / Z:"
XYZLabel.TextXAlignment = Enum.TextXAlignment.Left
XYZLabel.ZIndex = 10

local XBox = Instance.new("TextBox", TeleFrame)
XBox.Size = UDim2.new(0.32, -6, 0, 26)
XBox.Position = UDim2.new(0, 0, 0, 20)
XBox.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
XBox.TextColor3 = C.cyan
XBox.TextSize = 11
XBox.Font = Enum.Font.GothamBold
XBox.PlaceholderText = "X"
XBox.PlaceholderColor3 = C.dim
XBox.Text = ""
XBox.ClearTextOnFocus = false
XBox.BorderSizePixel = 0
XBox.ZIndex = 10
corner(XBox, 6)

local YBox = Instance.new("TextBox", TeleFrame)
YBox.Size = UDim2.new(0.32, -6, 0, 26)
YBox.Position = UDim2.new(0.34, 0, 0, 20)
YBox.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
YBox.TextColor3 = C.cyan
YBox.TextSize = 11
YBox.Font = Enum.Font.GothamBold
YBox.PlaceholderText = "Y"
YBox.PlaceholderColor3 = C.dim
YBox.Text = ""
YBox.ClearTextOnFocus = false
YBox.BorderSizePixel = 0
YBox.ZIndex = 10
corner(YBox, 6)

local ZBox = Instance.new("TextBox", TeleFrame)
ZBox.Size = UDim2.new(0.32, -6, 0, 26)
ZBox.Position = UDim2.new(0.68, 0, 0, 20)
ZBox.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
ZBox.TextColor3 = C.cyan
ZBox.TextSize = 11
ZBox.Font = Enum.Font.GothamBold
ZBox.PlaceholderText = "Z"
ZBox.PlaceholderColor3 = C.dim
ZBox.Text = ""
ZBox.ClearTextOnFocus = false
ZBox.BorderSizePixel = 0
ZBox.ZIndex = 10
corner(ZBox, 6)

local BtnGetPos = btn(TeleFrame, UDim2.new(0.49, -2, 0, 26), UDim2.new(0, 0, 0, 52), "📌 Lấy tọa độ")
BtnGetPos.TextColor3 = C.cyan
BtnGetPos.TextSize = 10

local BtnTeleCoord = btn(TeleFrame, UDim2.new(0.49, -2, 0, 26), UDim2.new(0.51, 2, 0, 52), "🚀 TELE")
BtnTeleCoord.TextColor3 = C.grn
BtnTeleCoord.TextSize = 10

local IslandLabel = Instance.new("TextLabel", TeleFrame)
IslandLabel.Size = UDim2.new(1, 0, 0, 16)
IslandLabel.Position = UDim2.new(0, 0, 0, 84)
IslandLabel.BackgroundTransparency = 1
IslandLabel.TextColor3 = C.dim
IslandLabel.TextSize = 10
IslandLabel.Font = Enum.Font.GothamBold
IslandLabel.Text = "Tele đảo:"
IslandLabel.TextXAlignment = Enum.TextXAlignment.Left
IslandLabel.ZIndex = 10

local IslandRow = Instance.new("Frame", TeleFrame)
IslandRow.Size = UDim2.new(1, 0, 0, 32)
IslandRow.Position = UDim2.new(0, 0, 0, 104)
IslandRow.BackgroundTransparency = 1
IslandRow.ZIndex = 10

local IslandButtons = {}
for i = 1, 5 do
    local b = btn(IslandRow, UDim2.new(0.19, 0, 1, 0), UDim2.new((i-1) * 0.2025, 0, 0, 0), "Đảo "..i)
    b.TextColor3 = Color3.fromRGB(230, 230, 240)
    b.TextSize = 10
    IslandButtons[i] = b
    b.MouseButton1Click:Connect(function()
        for j, b2 in pairs(IslandButtons) do
            if j == i then
                b2.BackgroundColor3 = Color3.fromRGB(50, 45, 30)
                b2.TextColor3 = C.gold
            else
                b2.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
                b2.TextColor3 = Color3.fromRGB(230, 230, 240)
            end
        end
        task.spawn(function() TeleportToPosition(Islands[i].pos, Islands[i].name) end)
    end)
end
IslandButtons[1].BackgroundColor3 = Color3.fromRGB(50, 45, 30)
IslandButtons[1].TextColor3 = C.gold

-- ================= ESP FRAME =================
local ESPFrame = Instance.new("Frame", Main)
ESPFrame.Size = UDim2.new(0, ContentW, 1, -50)
ESPFrame.Position = UDim2.new(0, ContentX, 0, 44)
ESPFrame.BackgroundTransparency = 1
ESPFrame.Visible = false
ESPFrame.ZIndex = 5

local BtnESPPlayer = btn(ESPFrame, UDim2.new(1, 0, 0, 32), UDim2.new(0, 0, 0, 0), "👤 ESP PLAYERS: OFF")
BtnESPPlayer.TextColor3 = C.cyan

local BtnESPFish = btn(ESPFrame, UDim2.new(1, 0, 0, 32), UDim2.new(0, 0, 0, 38), "🐟 ESP FISH: OFF")
BtnESPFish.TextColor3 = C.gold

local BtnESPZeno = btn(ESPFrame, UDim2.new(1, 0, 0, 32), UDim2.new(0, 0, 0, 76), "⚡ ESP ZENO: OFF")
BtnESPZeno.TextColor3 = C.purple

-- ================= STATUS =================
local Status = Instance.new("TextLabel", Main)
Status.Size = UDim2.new(1, -20, 0, 16)
Status.Position = UDim2.new(0, 10, 1, -18)
Status.BackgroundTransparency = 1
Status.TextColor3 = Color3.fromRGB(220, 220, 220)
Status.TextSize = 10
Status.Font = Enum.Font.Gotham
Status.Text = "● Sẵn sàng"
Status.TextXAlignment = Enum.TextXAlignment.Left
Status.ZIndex = 10

-- ================= TOGGLE =================
local ToggleBtn = Instance.new("TextButton", S)
ToggleBtn.Size = UDim2.new(0, 50, 0, 50)
ToggleBtn.Position = UDim2.new(0, 20, 0.5, -25)
ToggleBtn.BackgroundColor3 = C.bg2
ToggleBtn.TextColor3 = C.gold
ToggleBtn.TextSize = 24
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Text = "⚡"
ToggleBtn.BorderSizePixel = 0
ToggleBtn.Visible = false
ToggleBtn.ZIndex = 100
corner(ToggleBtn, 25)
stroke(ToggleBtn, C.gold, 2)
grad(ToggleBtn, Color3.fromRGB(45,38,25), C.bg2)
drag(ToggleBtn)

-- ================= BIẾN =================
_G.AutoFish = false; _G.AutoSkill = false; _G.AutoMini = false
_G.LockPos = false; _G.AnchorPos = nil
_G.ForceUnlock = false; _G.AutoSell = false
_G.SellTarget = 50
_G.ESPPlayer = false; _G.ESPFish = false; _G.ESPZeno = false

-- ================= PLAYLIST =================
local Playlist = {
    {name = "Ainsi Bas La Vida",          id = "90089940136467"},
    {name = "Her Boyfriend",              id = "105828916140935"},
    {name = "Misery",                     id = "86317637164248"},
    {name = "Tokoyi",                     id = "110398344352815"},
    {name = "Sơn Thủy Trùng Mây",         id = "805101300149129"},
    {name = "Vũ Trụ Có Anh",              id = "9555946575863"},
    {name = "Phép Màu Tình Yêu",          id = "99072612944031"},
    {name = "Quan Sơn Tửu",               id = "95280153572320"},
    {name = "Xích Linh Mix v2",           id = "114524646066441"},
    {name = "Raniy",                      id = "79277371759525"},
    {name = "Đảo Không Người",            id = "130495965769206"},
    {name = "Mạc Vấn Quy Kỳ",             id = "1082967212515595"},
    {name = "ĐÊM NÀY TRAO CHO ANH",       id = "135696731317179"},
    {name = "Trả Cho Anh",                id = "71214605813266"},
    {name = "Không Còn Gì Để Nói",        id = "99755869422778"},
    {name = "Khó Mà Quên Được Em",        id = "89127736411108"},
    {name = "Chạm Tay Vào Không Trung",   id = "105328945091287"},
    {name = "Em Đừng Quay Lại Nữa",       id = "77962573271495"},
    {name = "Quên Em Không Dễ Đâu",       id = "85262807728337"},
    {name = "Lạc Giữa Mây Trời",          id = "139318501029364"},
    {name = "Heavenly Jumpstyle",         id = "139945126932727"},
    {name = "In the Dark",                id = "75940515128169"},
    {name = "Dreamy Twilight",            id = "140511755680557"},
    {name = "Frappes and Chill",          id = "139585050089396"},
    {name = "Cosmic Calm",                id = "110053536427461"},
    {name = "Gentle Twilight",            id = "119210476046990"},
    {name = "Late Evening Chill",         id = "109988352289854"},
    {name = "All Nighter",                id = "137185846056595"},
    {name = "Deep Space",                 id = "107001608498251"},
    {name = "Moonlit Whispers",           id = "139824343487770"},
    {name = "Golden Hour Chill",          id = "97266136342861"},
    {name = "Winter LoFi Chill",          id = "84876808928610"},
    {name = "Dust in Sunlight",           id = "78783219474740"},
    {name = "Peaceful Lofi Vibes",        id = "117529154794878"},
    {name = "Sunrise and Chill",          id = "110414944838702"},
    {name = "Orbit",                      id = "118931742913095"},
    {name = "Soothing Sounds",            id = "137734857616833"},
    {name = "Nắng Dưới Chân Mây",         id = "76908132937245"},
}
local CurrentSong = 1

-- ================= MUSIC FRAME =================
local MusicFrame = Instance.new("Frame", Main)
MusicFrame.Size = UDim2.new(0, ContentW, 1, -50)
MusicFrame.Position = UDim2.new(0, ContentX, 0, 44)
MusicFrame.BackgroundTransparency = 1
MusicFrame.Visible = false
MusicFrame.ZIndex = 5

local BGM = Instance.new("Sound")
BGM.Name = "DatBeoBGM"
BGM.Looped = false
BGM.Volume = 0.5
BGM.Parent = Sound

local IsLoading = false

local function LoadSong(index)
    if IsLoading then return end
    IsLoading = true
    if index > #Playlist then index = 1 end
    if index < 1 then index = #Playlist end
    CurrentSong = index
    pcall(function() BGM:Stop() end)
    task.wait(0.05)
    BGM.SoundId = "rbxassetid://" .. Playlist[index].id
    BGM.TimePosition = 0
    pcall(function() BGM:Play() end)
    if _G.MusicNowPlaying then
        _G.MusicNowPlaying.Text = "♪ " .. Playlist[index].name
    end
    task.wait(0.3)
    IsLoading = false
end

local function NextSong() LoadSong(CurrentSong + 1) end
local function PrevSong() LoadSong(CurrentSong - 1) end

BGM.Ended:Connect(function()
    task.wait(0.2)
    NextSong()
end)

LoadSong(1)

local NowPlaying = Instance.new("TextLabel", MusicFrame)
NowPlaying.Size = UDim2.new(1, 0, 0, 24)
NowPlaying.Position = UDim2.new(0, 0, 0, 0)
NowPlaying.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
NowPlaying.BackgroundTransparency = 0.2
NowPlaying.TextColor3 = C.gold
NowPlaying.TextSize = 11
NowPlaying.Font = Enum.Font.GothamBold
NowPlaying.Text = "♪ " .. Playlist[1].name
NowPlaying.TextTruncate = Enum.TextTruncate.AtEnd
NowPlaying.ZIndex = 10
corner(NowPlaying, 6)
_G.MusicNowPlaying = NowPlaying

local SearchBox = Instance.new("TextBox", MusicFrame)
SearchBox.Size = UDim2.new(1, 0, 0, 26)
SearchBox.Position = UDim2.new(0, 0, 0, 28)
SearchBox.BackgroundColor3 = Color3.fromRGB(40, 45, 60)
SearchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SearchBox.PlaceholderText = "🔍 Tìm bài..."
SearchBox.PlaceholderColor3 = Color3.fromRGB(140, 150, 180)
SearchBox.Text = ""
SearchBox.TextSize = 11
SearchBox.Font = Enum.Font.Gotham
SearchBox.ClearTextOnFocus = false
SearchBox.BorderSizePixel = 0
SearchBox.ZIndex = 10
corner(SearchBox, 6)
stroke(SearchBox, C.cyan, 1.5, 0.5)

local MScroll = Instance.new("ScrollingFrame", MusicFrame)
MScroll.Size = UDim2.new(1, 0, 1, -140)
MScroll.Position = UDim2.new(0, 0, 0, 60)
MScroll.BackgroundColor3 = Color3.fromRGB(20, 22, 32)
MScroll.BorderSizePixel = 0
MScroll.ScrollBarThickness = 6
MScroll.ScrollBarImageColor3 = C.cyan
MScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
MScroll.ZIndex = 10
corner(MScroll, 8)

local MList = Instance.new("UIListLayout", MScroll)
MList.Padding = UDim.new(0, 3)
MList.SortOrder = Enum.SortOrder.LayoutOrder

local MControl = Instance.new("Frame", MusicFrame)
MControl.Size = UDim2.new(1, 0, 0, 32)
MControl.Position = UDim2.new(0, 0, 1, -34)
MControl.BackgroundTransparency = 1
MControl.ZIndex = 10

local BtnMPrev = btn(MControl, UDim2.new(0.32, -3, 1, 0), UDim2.new(0, 0, 0, 0), "◀")
BtnMPrev.TextColor3 = C.cyan
BtnMPrev.TextSize = 15

local BtnMPlay = btn(MControl, UDim2.new(0.32, -3, 1, 0), UDim2.new(0.34, 0, 0, 0), "▶ PHÁT")
BtnMPlay.TextColor3 = C.grn
BtnMPlay.TextSize = 11

local BtnMNext = btn(MControl, UDim2.new(0.32, -3, 1, 0), UDim2.new(0.68, 0, 0, 0), "▶")
BtnMNext.TextColor3 = C.cyan
BtnMNext.TextSize = 15

local filteredList = Playlist

local function renderPlaylist(list)
    for _, child in ipairs(MScroll:GetChildren()) do
        if child:IsA("TextButton") then child:Destroy() end
    end
    filteredList = list
    for i, song in ipairs(list) do
        local isPlaying = (Playlist[CurrentSong] == song)
        local songBtn = Instance.new("TextButton", MScroll)
        songBtn.Size = UDim2.new(1, -8, 0, 30)
        songBtn.BackgroundColor3 = isPlaying and Color3.fromRGB(60, 45, 25) or Color3.fromRGB(35, 38, 50)
        songBtn.TextColor3 = isPlaying and C.gold or Color3.fromRGB(230, 230, 245)
        songBtn.Text = "  " .. i .. ". " .. song.name
        songBtn.TextSize = 10
        songBtn.Font = Enum.Font.GothamBold
        songBtn.TextXAlignment = Enum.TextXAlignment.Left
        songBtn.LayoutOrder = i
        songBtn.BorderSizePixel = 0
        songBtn.ZIndex = 11
        corner(songBtn, 6)
        songBtn.MouseButton1Click:Connect(function()
            for idx, s in ipairs(Playlist) do
                if s == song then LoadSong(idx) break end
            end
            renderPlaylist(filteredList)
        end)
    end
    MScroll.CanvasSize = UDim2.new(0, 0, 0, #list * 34 + 10)
end

SearchBox:GetPropertyChangedSignal("Text"):Connect(function()
    local q = string.lower(SearchBox.Text)
    if q == "" then renderPlaylist(Playlist) return end
    local results = {}
    for _, song in ipairs(Playlist) do
        if string.find(string.lower(song.name), q, 1, true) then
            table.insert(results, song)
        end
    end
    renderPlaylist(results)
end)

BtnMPrev.MouseButton1Click:Connect(function() PrevSong() renderPlaylist(filteredList) end)
BtnMNext.MouseButton1Click:Connect(function() NextSong() renderPlaylist(filteredList) end)
BtnMPlay.MouseButton1Click:Connect(function()
    if BGM.Playing then
        BGM:Pause()
        BtnMPlay.Text = "⏸ DỪNG"
        BtnMPlay.BackgroundColor3 = Color3.fromRGB(55, 35, 35)
        BtnMPlay.TextColor3 = C.red
    else
        pcall(function() BGM:Play() end)
        BtnMPlay.Text = "▶ PHÁT"
        BtnMPlay.BackgroundColor3 = Color3.fromRGB(35, 40, 55)
        BtnMPlay.TextColor3 = C.grn
    end
end)

-- ================= TELE FUNCTION =================
function TeleportToPosition(targetPos, name)
    local char = P.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    Status.Text = "● 🌀 Đang dịch chuyển..."
    Status.TextColor3 = C.cyan
    local newChar
    local conn = P.CharacterAdded:Connect(function(c) newChar = c end)
    hum.Health = 0
    local startWait = tick()
    while not newChar and tick() - startWait < 10 do task.wait(0.1) end
    if conn then conn:Disconnect() end
    if not newChar then Status.Text = "● ❌ Lỗi hồi sinh" return end
    local newHrp = newChar:WaitForChild("HumanoidRootPart", 5)
    local newHum = newChar:WaitForChild("Humanoid", 5)
    if not newHrp or not newHum then return end
    task.wait(0.5)
    local targetV3 = Vector3.new(targetPos.X, targetPos.Y + 10, targetPos.Z)
    pcall(function()
        newHrp.Anchored = true
        newHrp.CFrame = CFrame.new(targetV3)
    end)
    Status.Text = "● 🔒 Khóa 3s..."
    Status.TextColor3 = C.gold
    local lockEnd = tick() + 3
    while tick() < lockEnd do
        pcall(function()
            if newHrp and newHrp.Parent then
                newHrp.Anchored = true
                newHrp.CFrame = CFrame.new(targetV3)
                newHrp.Velocity = Vector3.new(0, 0, 0)
            end
            if newHum and newHum.Parent then
                newHum.WalkSpeed = 0
                newHum.JumpPower = 0
                newHum.PlatformStand = true
            end
        end)
        task.wait(0.01)
    end
    Status.Text = "● ⚡ Nhả khóa 2s..."
    Status.TextColor3 = C.red
    local unlockEnd = tick() + 2
    local toggle = true
    while tick() < unlockEnd do
        pcall(function()
            if newHrp and newHrp.Parent then
                newHrp.Anchored = toggle
                if toggle then newHrp.CFrame = CFrame.new(targetV3) end
            end
            if newHum and newHum.Parent then
                newHum.WalkSpeed = toggle and 0 or 16
                newHum.JumpPower = toggle and 0 or 50
                newHum.PlatformStand = toggle
            end
        end)
        toggle = not toggle
        task.wait(0.02)
    end
    pcall(function()
        if newHrp and newHrp.Parent then newHrp.Anchored = false end
        if newHum and newHum.Parent then
            newHum.WalkSpeed = 16
            newHum.JumpPower = 50
            newHum.PlatformStand = false
        end
    end)
    Status.Text = "● ✅ Đã đến " .. (name or "tọa độ")
    Status.TextColor3 = C.grn
end

-- ================= SELL FUNCTION =================
local function getFish()
    local count = 0
    local function check(container)
        if container then
            for _, item in ipairs(container:GetChildren()) do
                if item:IsA("Tool") then
                    local name = string.lower(item.Name)
                    if not (name:find("rod") or name:find("cần") or name:find("bait")
                        or name:find("mồi") or name:find("potion") or name:find("thuốc")
                        or name:find("license") or name:find("gps") or name:find("radar")
                        or name:find("pass")) then
                        count = count + 1
                    end
                end
            end
        end
    end
    check(P.Backpack)
    check(P.Character)
    return count
end

local function clickBtn(keys)
    local inset = game:GetService("GuiService"):GetGuiInset()
    for _, obj in pairs(P.PlayerGui:GetDescendants()) do
        if (obj:IsA("TextButton") or obj:IsA("TextLabel")) and obj.Visible then
            local txt = string.lower(obj.Text or "")
            for _, kw in ipairs(keys) do
                if txt:find(kw) then
                    local pos, sz = obj.AbsolutePosition, obj.AbsoluteSize
                    local cx, cy = pos.X + sz.X/2 + inset.X, pos.Y + sz.Y/2 + inset.Y
                    VIM:SendMouseButtonEvent(cx, cy, 0, true, game, 1)
                    task.wait(0.05)
                    VIM:SendMouseButtonEvent(cx, cy, 0, false, game, 1)
                    return true
                end
            end
        end
    end
    return false
end

local function SellAll()
    local char = P.Character
    if not char then return false, "Không có char" end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hrp or not hum then return false, "Lỗi" end
    local oldCFrame = hrp.CFrame
    local oldWalkSpeed = hum.WalkSpeed
    local npc, shortest = nil, math.huge
    for _, g in pairs(workspace:GetDescendants()) do
        if g:IsA("Model") or g:IsA("BasePart") then
            local n = string.lower(g.Name)
            if n:find("fish") then
                local p = g:IsA("BasePart") and g
                    or g:FindFirstChild("HumanoidRootPart")
                    or g:FindFirstChildWhichIsA("BasePart")
                if p then
                    local d = (p.Position - hrp.Position).Magnitude
                    if d < shortest then shortest = d; npc = p end
                end
            end
        end
    end
    if not npc then return false, "Không có NPC" end
    local npcPos = npc.Position
    local standPos = npcPos + (hrp.Position - npcPos).Unit * 4
    standPos = Vector3.new(standPos.X, npcPos.Y, standPos.Z)
    Status.Text = "● Bay đến NPC..."
    Status.TextColor3 = C.cyan
    local flyStart = tick()
    while tick() - flyStart < 15 do
        if not hrp or not hrp.Parent then break end
        local dist = (standPos - hrp.Position).Magnitude
        if dist < 3 then break end
        local dir = (standPos - hrp.Position).Unit
        pcall(function()
            hrp.CFrame = CFrame.new(hrp.Position + dir * 1.6)
            hrp.Velocity = Vector3.new(0, 0, 0)
        end)
        task.wait(0.05)
    end
    hum.WalkSpeed = 0
    hrp.Anchored = true
    hrp.CFrame = CFrame.new(standPos, Vector3.new(npcPos.X, standPos.Y, npcPos.Z))
    task.wait(0.5)
    local prompt = nil
    for _, p in pairs(workspace:GetDescendants()) do
        if p:IsA("ProximityPrompt") and p.Enabled then
            local part = p.Parent
            if part and part:IsA("BasePart") then
                if (part.Position - hrp.Position).Magnitude < 15 then
                    prompt = p break
                end
            end
        end
    end
    local fCount = getFish()
    local loopCount = 0
    local sellStart = tick()
    repeat
        if tick() - sellStart >= 25 then break end
        loopCount = loopCount + 1
        if prompt then pcall(function() fireproximityprompt(prompt) end) end
        rndDelay(2, 0.5)
        K(Enum.KeyCode.E, 0.1)
        rndDelay(0.5, 0.2)
        clickBtn({"bán tất cả", "sell all", "bán", "sell", "confirm", "đồng ý"})
        rndDelay(1.5, 0.5)
        fCount = getFish()
    until fCount == 0 or loopCount >= 10
    Status.Text = "● 🛫 Bay về chỗ cũ..."
    Status.TextColor3 = C.cyan
    local returnPos = oldCFrame.Position
    hrp.Anchored = false
    hum.WalkSpeed = 0
    hum.JumpPower = 0
    local backStart = tick()
    while tick() - backStart < 5 do
        if not hrp or not hrp.Parent then break end
        local curPos = hrp.Position
        local dist = (returnPos - curPos).Magnitude
        if dist < 3 then break end
        local dir = (returnPos - curPos).Unit
        local step = dir * 4.5
        pcall(function()
            hrp.CFrame = CFrame.new(curPos + step, curPos + step + hrp.CFrame.LookVector)
            hrp.Velocity = Vector3.new(0, 0, 0)
        end)
        task.wait(0.05)
    end
    pcall(function()
        hrp.CFrame = oldCFrame
        hrp.Velocity = Vector3.new(0, 0, 0)
    end)
    hum.WalkSpeed = oldWalkSpeed
    hum.JumpPower = 50
    hrp.Anchored = false
    task.wait(0.3)
    Status.Text = "● ✅ Đã bán + bay về"
    Status.TextColor3 = C.grn
    return true, "Đã bán " .. loopCount .. " lần"
end

local function SaveAnchor()
    local c = P.Character
    if c and c:FindFirstChild("HumanoidRootPart") then
        _G.AnchorPos = c.HumanoidRootPart.Position
        _G.LockPos = true
    end
end

task.spawn(function()
    while true do
        task.wait(0.2)
        if not _G.ForceUnlock and _G.LockPos and _G.AnchorPos then
            local c = P.Character
            if c and c:FindFirstChild("HumanoidRootPart") then
                if (c.HumanoidRootPart.Position - _G.AnchorPos).Magnitude > 3 then
                    c.HumanoidRootPart.CFrame = CFrame.new(_G.AnchorPos)
                end
            end
        end
    end
end)

task.spawn(function()
    while true do
        task.wait(0.05)
        if _G.AutoSkill or _G.AutoFish then
            K(Enum.KeyCode.Z, 0.01); K(Enum.KeyCode.X, 0.01)
            K(Enum.KeyCode.C, 0.01); K(Enum.KeyCode.V, 0.01)
        end
    end
end)

task.spawn(function()
    while true do
        task.wait(0.05)
        if _G.AutoMini or _G.AutoFish then
            K(Enum.KeyCode.W, 0.02); K(Enum.KeyCode.A, 0.05); K(Enum.KeyCode.D, 0.07)
        end
    end
end)

local function CheckFish()
    local pg = P:FindFirstChild("PlayerGui")
    if pg then
        for _, g in pairs(pg:GetDescendants()) do
            if (g:IsA("TextLabel") or g:IsA("TextButton")) then
                if string.find(string.lower(tostring(g.Text or "")), "fish caught") then return true end
            end
        end
    end
    return false
end

-- ================= AUTO FISH (FIX) =================
task.spawn(function()
    while true do
        task.wait(0.1)
        if _G.AutoFish then
            Status.Text = "● Thả cần..."
            Status.TextColor3 = C.cyan
            Click(0.5)
            task.wait(0.3)
            Click(0.2)
            Status.Text = "● Chờ cá..."
            local startT = tick()
            local caught = false
            while _G.AutoFish and (tick() - startT < 25) do
                if CheckFish() then caught = true break end
                task.wait(0.3 + math.random() * 0.3)
            end
            if caught then
                local currentFish = getFish()
                Status.Text = "● Đã câu! Kho: " .. currentFish
                Status.TextColor3 = C.grn
                task.wait(1)
                if _G.AutoSell and currentFish >= _G.SellTarget then
                    local wasLocked = _G.LockPos
                    _G.LockPos = false; _G.ForceUnlock = true
                    task.wait(0.5)
                    SellAll()
                    task.wait(1.5)
                    _G.ForceUnlock = false
                    if wasLocked then SaveAnchor() end
                end
                task.wait(0.5)
            else
                Status.Text = "● ⏰ Hết thời gian"
                Status.TextColor3 = C.red
                task.wait(0.5)
            end
        else
            task.wait(0.2)
        end
    end
end)

-- ================= ESP =================
local function createESP(target, color, name)
    if not target then return end
    local h = Instance.new("Highlight")
    h.Name = "DatBeoESP_" .. name
    h.Adornee = target
    h.FillColor = color
    h.FillTransparency = 0.5
    h.OutlineColor = color
    h.OutlineTransparency = 0
    h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    h.Parent = target
end

local function clearESP(name)
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Highlight") and v.Name == "DatBeoESP_" .. name then v:Destroy() end
    end
end

local function createPlayerESP(character, playerName)
    if not character or not character.Parent then return end
    local head = character:FindFirstChild("Head")
    if not head then return end
    if head:FindFirstChild("DatBeoNameESP") then head.DatBeoNameESP:Destroy() end
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "DatBeoNameESP"
    billboard.Size = UDim2.new(0, 200, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.AlwaysOnTop = true
    billboard.Parent = head
    local frame = Instance.new("Frame", billboard)
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    frame.BackgroundTransparency = 0.4
    frame.BorderSizePixel = 0
    corner(frame, 6)
    local nameLabel = Instance.new("TextLabel", frame)
    nameLabel.Size = UDim2.new(1, 0, 1, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.TextColor3 = C.cyan
    nameLabel.TextSize = 14
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.Text = playerName
    nameLabel.TextStrokeTransparency = 0
end

task.spawn(function()
    while true do
        task.wait(1)
        if _G.ESPPlayer then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= P and plr.Character then
                    if not plr.Character:FindFirstChild("DatBeoESP_Player") then
                        createESP(plr.Character, C.cyan, "Player")
                    end
                    createPlayerESP(plr.Character, plr.Name)
                end
            end
        end
        if _G.ESPFish then
            for _, g in pairs(workspace:GetDescendants()) do
                if g:IsA("Model") and string.lower(g.Name):find("fish merchant") then
                    if not g:FindFirstChild("DatBeoESP_Fish") then
                        createESP(g, C.gold, "Fish")
                    end
                end
            end
        end
        if _G.ESPZeno then
            for _, g in pairs(workspace:GetDescendants()) do
                if (g:IsA("Model") or g:IsA("BasePart")) and string.lower(g.Name):find("zeno") then
                    if not g:FindFirstChild("DatBeoESP_Zeno") then
                        createESP(g, C.purple, "Zeno")
                    end
                end
            end
        end
    end
end)

-- ================= SWITCH TAB =================
local function SwitchTab(tab)
    FarmFrame.Visible = (tab == "farm")
    SellFrame.Visible = (tab == "sell")
    TeleFrame.Visible = (tab == "tele")
    ESPFrame.Visible = (tab == "esp")
    MusicFrame.Visible = (tab == "music")

    BtnTabFarm.BackgroundColor3 = Color3.fromRGB(30, 30, 40); BtnTabFarm.TextColor3 = C.dim
    BtnTabSell.BackgroundColor3 = Color3.fromRGB(30, 30, 40); BtnTabSell.TextColor3 = C.dim
    BtnTabTele.BackgroundColor3 = Color3.fromRGB(30, 30, 40); BtnTabTele.TextColor3 = C.dim
    BtnTabESP.BackgroundColor3 = Color3.fromRGB(30, 30, 40); BtnTabESP.TextColor3 = C.dim
    BtnTabMusic.BackgroundColor3 = Color3.fromRGB(30, 30, 40); BtnTabMusic.TextColor3 = C.dim

    if tab == "farm" then
        BtnTabFarm.BackgroundColor3 = Color3.fromRGB(35, 55, 45); BtnTabFarm.TextColor3 = C.grn
    elseif tab == "sell" then
        BtnTabSell.BackgroundColor3 = Color3.fromRGB(50, 45, 30); BtnTabSell.TextColor3 = C.gold
    elseif tab == "tele" then
        BtnTabTele.BackgroundColor3 = Color3.fromRGB(30, 40, 55); BtnTabTele.TextColor3 = C.cyan
    elseif tab == "esp" then
        BtnTabESP.BackgroundColor3 = Color3.fromRGB(40, 30, 55); BtnTabESP.TextColor3 = C.purple
    elseif tab == "music" then
        BtnTabMusic.BackgroundColor3 = Color3.fromRGB(50, 35, 55); BtnTabMusic.TextColor3 = C.gold
    end
end

BtnTabFarm.MouseButton1Click:Connect(function() SwitchTab("farm") end)
BtnTabSell.MouseButton1Click:Connect(function() SwitchTab("sell") end)
BtnTabTele.MouseButton1Click:Connect(function() SwitchTab("tele") end)
BtnTabESP.MouseButton1Click:Connect(function() SwitchTab("esp") end)
BtnTabMusic.MouseButton1Click:Connect(function() SwitchTab("music") end)

-- ================= LOCK MENU =================
BtnLockMenu.MouseButton1Click:Connect(function()
    _G.MenuLocked = not _G.MenuLocked
    if _G.MenuLocked then
        BtnLockMenu.Text = "🔒"
        BtnLockMenu.BackgroundColor3 = Color3.fromRGB(50, 35, 35)
        BtnLockMenu.TextColor3 = C.red
        MS.Color = C.red
    else
        BtnLockMenu.Text = "🔓"
        BtnLockMenu.BackgroundColor3 = Color3.fromRGB(30, 40, 50)
        BtnLockMenu.TextColor3 = C.grn
        MS.Color = C.gold
    end
end)

-- ================= BUTTON EVENTS =================
BtnAuto.MouseButton1Click:Connect(function()
    _G.AutoFish = not _G.AutoFish
    if _G.AutoFish then
        BtnAuto.Text = "🎣 AUTO CÂU: ON"; BtnAuto.TextColor3 = C.red
        _G.AutoSkill = true; _G.AutoMini = true
        BtnSkill.Text = "⚔️ SKILL: ON"; BtnSkill.TextColor3 = C.red
        BtnMini.Text = "🎮 MINI: ON"; BtnMini.TextColor3 = C.red
        if not _G.ForceUnlock then
            SaveAnchor()
            BtnLock.Text = "🔒 KHÓA: ON"; BtnLock.TextColor3 = C.red
        end
    else
        BtnAuto.Text = "🎣 AUTO CÂU: OFF"; BtnAuto.TextColor3 = C.grn
        _G.AutoSkill = false; _G.AutoMini = false
        BtnSkill.Text = "⚔️ SKILL: OFF"; BtnSkill.TextColor3 = C.grn
        BtnMini.Text = "🎮 MINI: OFF"; BtnMini.TextColor3 = C.grn
    end
end)

BtnSkill.MouseButton1Click:Connect(function()
    _G.AutoSkill = not _G.AutoSkill
    BtnSkill.Text = _G.AutoSkill and "⚔️ SKILL: ON" or "⚔️ SKILL: OFF"
    BtnSkill.TextColor3 = _G.AutoSkill and C.red or C.grn
end)

BtnMini.MouseButton1Click:Connect(function()
    _G.AutoMini = not _G.AutoMini
    BtnMini.Text = _G.AutoMini and "🎮 MINI: ON" or "🎮 MINI: OFF"
    BtnMini.TextColor3 = _G.AutoMini and C.red or C.grn
end)

BtnLock.MouseButton1Click:Connect(function()
    if _G.LockPos then
        _G.LockPos = false; _G.AnchorPos = nil
        BtnLock.Text = "🔒 KHÓA: OFF"; BtnLock.TextColor3 = C.grn
    else
        SaveAnchor()
        BtnLock.Text = "🔒 KHÓA: ON"; BtnLock.TextColor3 = C.red
    end
end)

BtnUnlock.MouseButton1Click:Connect(function()
    _G.LockPos = false; _G.AnchorPos = nil; _G.ForceUnlock = true
    BtnLock.Text = "🔒 KHÓA: OFF"; BtnLock.TextColor3 = C.grn
    task.wait(0.5); _G.ForceUnlock = false
end)

BtnStealth.MouseButton1Click:Connect(function()
    STEALTH.Enabled = not STEALTH.Enabled
    BtnStealth.Text = STEALTH.Enabled and "🛡️ STEALTH: ON" or "🛡️ STEALTH: OFF"
    BtnStealth.TextColor3 = STEALTH.Enabled and C.grn or C.red
end)

BtnTestClick.MouseButton1Click:Connect(function()
    Status.Text = "● Test click..."
    Status.TextColor3 = C.cyan
    Click(0.3)
    task.wait(0.5)
    Status.Text = "● Đã click xong"
    Status.TextColor3 = C.grn
end)

BtnSell.MouseButton1Click:Connect(function()
    task.spawn(function()
        local wasLocked = _G.LockPos
        _G.LockPos = false; _G.ForceUnlock = true
        task.wait(0.5); SellAll(); task.wait(0.5)
        _G.ForceUnlock = false
        if wasLocked then SaveAnchor() end
    end)
end)

BtnSellMax.MouseButton1Click:Connect(function()
    task.spawn(function()
        local wasLocked = _G.LockPos
        _G.LockPos = false; _G.ForceUnlock = true
        task.wait(0.5); SellAll(); task.wait(0.5)
        _G.ForceUnlock = false
        if wasLocked then SaveAnchor() end
    end)
end)

local milestoneIdx = 4
local milestones = {10, 20, 30, 50}

BtnMilestone.MouseButton1Click:Connect(function()
    milestoneIdx = milestoneIdx + 1
    if milestoneIdx > #milestones then milestoneIdx = 1 end
    _G.SellTarget = milestones[milestoneIdx]
    BtnMilestone.Text = "🎯 MỐC BÁN: " .. _G.SellTarget .. " CÁ"
end)

BtnAutoSell.MouseButton1Click:Connect(function()
    _G.AutoSell = not _G.AutoSell
    BtnAutoSell.Text = _G.AutoSell and "💰 AUTO SELL: ON" or "💰 AUTO SELL: OFF"
    BtnAutoSell.TextColor3 = _G.AutoSell and C.red or C.gold
end)

BtnGetPos.MouseButton1Click:Connect(function()
    local char = P.Character
    if char then
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if hrp then
            local pos = hrp.Position
            XBox.Text = string.format("%.1f", pos.X)
            YBox.Text = string.format("%.1f", pos.Y)
            ZBox.Text = string.format("%.1f", pos.Z)
            Status.Text = "● 📌 Đã lấy tọa độ"
            Status.TextColor3 = C.cyan
        end
    end
end)

BtnTeleCoord.MouseButton1Click:Connect(function()
    local x = tonumber(XBox.Text)
    local y = tonumber(YBox.Text)
    local z = tonumber(ZBox.Text)
    if not x or not y or not z then
        Status.Text = "● ❌ Tọa độ không hợp lệ"
        Status.TextColor3 = C.red
        return
    end
    task.spawn(function()
        TeleportToPosition(Vector3.new(x, y, z), "tọa độ")
    end)
end)

BtnESPPlayer.MouseButton1Click:Connect(function()
    _G.ESPPlayer = not _G.ESPPlayer
    BtnESPPlayer.Text = _G.ESPPlayer and "👤 ESP PLAYERS: ON" or "👤 ESP PLAYERS: OFF"
    BtnESPPlayer.TextColor3 = _G.ESPPlayer and C.red or C.cyan
    if not _G.ESPPlayer then clearESP("Player") end
end)

BtnESPFish.MouseButton1Click:Connect(function()
    _G.ESPFish = not _G.ESPFish
    BtnESPFish.Text = _G.ESPFish and "🐟 ESP FISH: ON" or "🐟 ESP FISH: OFF"
    BtnESPFish.TextColor3 = _G.ESPFish and C.red or C.gold
    if not _G.ESPFish then clearESP("Fish") end
end)

BtnESPZeno.MouseButton1Click:Connect(function()
    _G.ESPZeno = not _G.ESPZeno
    BtnESPZeno.Text = _G.ESPZeno and "⚡ ESP ZENO: ON" or "⚡ ESP ZENO: OFF"
    BtnESPZeno.TextColor3 = _G.ESPZeno and C.red or C.purple
    if not _G.ESPZeno then clearESP("Zeno") end
end)

Close.MouseButton1Click:Connect(function()
    Main.Visible = false
    ToggleBtn.Visible = true
end)
ToggleBtn.MouseButton1Click:Connect(function()
    Main.Visible = not Main.Visible
    ToggleBtn.Visible = not Main.Visible
end)

-- ================= KHỞI ĐỘNG =================
Main.Visible = true
ToggleBtn.Visible = false
SwitchTab("farm")
renderPlaylist(Playlist)

pcall(function()
    game:GetService("StarterGui"):SetCore("SendNotification", {
        Title = "⚡ DATBEO SCRIPT",
        Text = "Script đã load - " .. #Playlist .. " bài nhạc 🎵",
        Duration = 5
    })
end)

print("[DatBeo] ✅ Script loaded - " .. #Playlist .. " bài nhạc")
