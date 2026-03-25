----------------------------------------------------------------------------------------------------------
-----------------------------------------Aba Intro-----------------------------------------------------
----------------------------------------------------------------------------------------------------------------

-- SERVIÇOS
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RedzHubIntro"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = PlayerGui

-- Fundo
local bg = Instance.new("Frame")
bg.Size = UDim2.fromScale(1,1)
bg.BackgroundColor3 = Color3.fromRGB(0,0,0)
bg.BackgroundTransparency = 1
bg.Parent = screenGui

-- Logo
local logo = Instance.new("TextLabel")
logo.Size = UDim2.new(0.6,0,0.2,0)
logo.Position = UDim2.new(0.5,0,-0.2,0)
logo.AnchorPoint = Vector2.new(0.5,0.5)
logo.BackgroundTransparency = 1
logo.Text = "SAGAZx HUB"
logo.TextScaled = true
logo.Font = Enum.Font.GothamBold
logo.TextColor3 = Color3.fromRGB(128,86,255)
logo.TextStrokeTransparency = 0.3
logo.TextStrokeColor3 = Color3.fromRGB(128,86,255)
logo.Parent = screenGui

-- Gradiente
local logoGradient = Instance.new("UIGradient")
logoGradient.Color = ColorSequence.new{
	ColorSequenceKeypoint.new(0, Color3.fromRGB(128,86,255)),
	ColorSequenceKeypoint.new(0.5, Color3.fromRGB(60,0,0)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(128,86,255))
}
logoGradient.Rotation = 45
logoGradient.Parent = logo

-- Subtítulo
local subtitle = Instance.new("TextLabel")
subtitle.Size = UDim2.new(0,200,0,25)
subtitle.Position = UDim2.new(0.5,0,0.58,0)
subtitle.AnchorPoint = Vector2.new(0.5,0.5)
subtitle.BackgroundTransparency = 1
subtitle.Text = "by SAGAZx"
subtitle.TextColor3 = Color3.fromRGB(128,86,255)
subtitle.TextTransparency = 1
subtitle.TextScaled = true
subtitle.Font = Enum.Font.Gotham
subtitle.Parent = screenGui

-- Barra de carregamento
local loadFrame = Instance.new("Frame")
loadFrame.Size = UDim2.new(0,0,0.02,0)
loadFrame.Position = UDim2.new(0.5,0,0.85,0)
loadFrame.AnchorPoint = Vector2.new(0.5,0)
loadFrame.BackgroundColor3 = Color3.fromRGB(128,86,255)
loadFrame.BorderSizePixel = 0
loadFrame.Parent = screenGui

-- FUNÇÃO TWEEN
local function tween(obj,time,props,style)
	local info = TweenInfo.new(time, style or Enum.EasingStyle.Quad)
	local tw = TweenService:Create(obj,info,props)
	tw:Play()
	return tw
end

-- Fade fundo
tween(bg,0.5,{BackgroundTransparency = 0})
task.wait(0.6)

-- Entrada logo
tween(logo,1,{
	Position = UDim2.new(0.5,0,0.4,0)
},Enum.EasingStyle.Quart)

task.wait(1)

-- Subtitulo
tween(subtitle,1,{TextTransparency = 0})

-- Barra
tween(loadFrame,2,{
	Size = UDim2.new(0.8,0,0.02,0)
},Enum.EasingStyle.Linear)

-- Som
local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://107004225739474"
sound.Volume = 2
sound.Parent = screenGui
sound:Play()

task.wait(2.5)

-- Fade saída
tween(bg,0.8,{BackgroundTransparency = 1})
tween(logo,0.8,{TextTransparency = 1})
tween(subtitle,0.8,{TextTransparency = 1})
tween(loadFrame,0.8,{BackgroundTransparency = 1})

task.wait(1)
screenGui:Destroy()

----------------------------------------------------------------------------------------------------------------
-----------------------------------------Aba Nome de entrada-----------------------------------------------------
----------------------------------------------------------------------------------------------------------------

local RE = ReplicatedStorage:WaitForChild("RE")
local NameEvent = RE:WaitForChild("1RPNam1eTex1t")
local ColorEvent = RE:WaitForChild("1RPNam1eColo1r")

-- Nome RP
NameEvent:FireServer("RolePlayName","SAGAZx HUB")

-- Cor nome
ColorEvent:FireServer("PickingRPNameColor",Color3.fromRGB(128,86,255))

-- Bio
NameEvent:FireServer("RolePlayBio","BEM-VINDO "..Player.DisplayName)

-- Cor Bio
ColorEvent:FireServer("PickingRPBioColor",Color3.fromRGB(87,191,255))