----------------------------------------------------------------------------------------------------------
-----------------------------------------Aba Intro-----------------------------------------------------
----------------------------------------------------------------------------------------------------------------

local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local LoadingConfig = {
    Duration = 8,
    FadeOutTime = 1.5,
    BarColor = Color3.fromRGB(255, 255, 255),
    BarGlow = Color3.fromRGB(200, 200, 200),
    BackgroundColor = Color3.fromRGB(0, 0, 0),
    TextColor = Color3.fromRGB(255, 255, 255),
    ParticleCount = 150,
    ParticleSpeed = 2,
    ParticleSize = 3,
    
    -- CONFIGURAÇÃO DE MÚSICA
    MusicID = 79120642849019,
    MusicVolume = 0.5,
    PlayMusic = true
}

local LoadingElements = {
    "Starting system...",
    "Loading interface...",
    "Loading scripts...",
    "Optimizing performance...",
    "Thank you for running...",
    "Finalizing loading...",
    "Ready to use!"
}

local Particles = {}
local AnimationConnections = {}
local LoadingMusic = nil

-- Função para criar e tocar a música
local function CreateLoadingMusic()
    if not LoadingConfig.PlayMusic then
        return nil
    end
    
    local sound = Instance.new("Sound")
    sound.Name = "LoadingMusic"
    sound.SoundId = "rbxassetid://" .. LoadingConfig.MusicID
    sound.Volume = LoadingConfig.MusicVolume
    sound.Looped = true
    sound.Parent = game:GetService("SoundService")
    
    sound:Play()
    LoadingMusic = sound
    
    return sound
end

-- Função para fazer fade out da música
local function FadeOutMusic(duration)
    if LoadingMusic then
        local fadeInfo = TweenInfo.new(duration, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        local fadeTween = TweenService:Create(LoadingMusic, fadeInfo, {Volume = 0})
        
        fadeTween:Play()
        fadeTween.Completed:Connect(function()
            if LoadingMusic then
                LoadingMusic:Stop()
                LoadingMusic:Destroy()
                LoadingMusic = nil
            end
        end)
    end
end

local function CreateStarParticle(parent, x, y)
    local star = Instance.new("Frame")
    star.Name = "StarParticle"
    star.Parent = parent
    star.BackgroundColor3 = Color3.fromRGB(180, 180, 180)
    star.BorderSizePixel = 0
    star.Size = UDim2.new(0, math.random(2, LoadingConfig.ParticleSize), 0, math.random(2, LoadingConfig.ParticleSize))
    star.Position = UDim2.new(0, x, 0, y)
    star.ZIndex = 8
    star.BackgroundTransparency = math.random(0, 50) / 100
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(1, 0)
    corner.Parent = star
    
    local gradient = Instance.new("UIGradient")
    gradient.Parent = star
    gradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(150, 150, 150)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255))
    })
    
    local particle = {
        frame = star,
        speedX = (math.random(-100, 100) / 100) * LoadingConfig.ParticleSpeed,
        speedY = (math.random(-100, 100) / 100) * LoadingConfig.ParticleSpeed,
        rotationSpeed = math.random(-5, 5),
        pulseSpeed = math.random(1, 3),
        opacity = math.random(30, 100) / 100
    }
    
    return particle
end

local function CreateParticleSystem(parent)
    local screenSize = workspace.CurrentCamera.ViewportSize
    
    for i = 1, LoadingConfig.ParticleCount do
        local x = math.random(0, screenSize.X)
        local y = math.random(0, screenSize.Y)
        local particle = CreateStarParticle(parent, x, y)
        table.insert(Particles, particle)
    end
end

local function UpdateParticles(parentFrame)
    local screenSize = workspace.CurrentCamera.ViewportSize
    
    for _, particle in pairs(Particles) do
        if particle.frame and particle.frame.Parent then
            local currentPos = particle.frame.Position
            local newX = currentPos.X.Offset + particle.speedX
            local newY = currentPos.Y.Offset + particle.speedY
            
            if newX < -10 then
                newX = screenSize.X + 10
            elseif newX > screenSize.X + 10 then
                newX = -10
            end
            
            if newY < -10 then
                newY = screenSize.Y + 10
            elseif newY > screenSize.Y + 10 then
                newY = -10
            end
            
            particle.frame.Position = UDim2.new(0, newX, 0, newY)
            particle.frame.Rotation = particle.frame.Rotation + particle.rotationSpeed
            
            local pulseValue = math.sin(tick() * particle.pulseSpeed) * 0.3 + 0.7
            particle.frame.BackgroundTransparency = 1 - (particle.opacity * pulseValue)
            
            local sizeMultiplier = 1 + (math.sin(tick() * particle.pulseSpeed * 0.5) * 0.2)
            particle.frame.Size = UDim2.new(0, LoadingConfig.ParticleSize * sizeMultiplier, 0, LoadingConfig.ParticleSize * sizeMultiplier)
        end
    end
end

local function CreateAdvancedBackground(parent)
    local bgFrame = Instance.new("Frame")
    bgFrame.Name = "AdvancedBackground"
    bgFrame.Parent = parent
    bgFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    bgFrame.BorderSizePixel = 0
    bgFrame.Size = UDim2.new(1, 0, 1, 0)
    bgFrame.Position = UDim2.new(0, 0, 0, 0)
    bgFrame.ZIndex = 1
    
    local gradient1 = Instance.new("UIGradient")
    gradient1.Parent = bgFrame
    gradient1.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(10, 10, 10)),
        ColorSequenceKeypoint.new(0.3, Color3.fromRGB(0, 0, 0)),
        ColorSequenceKeypoint.new(0.7, Color3.fromRGB(15, 15, 15)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 0, 0))
    })
    gradient1.Rotation = 45
    
    local animateGradient = TweenService:Create(
        gradient1,
        TweenInfo.new(4, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Rotation = 225}
    )
    animateGradient:Play()
    table.insert(AnimationConnections, animateGradient)
    
    local overlay = Instance.new("Frame")
    overlay.Name = "Overlay"
    overlay.Parent = bgFrame
    overlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    overlay.BorderSizePixel = 0
    overlay.Size = UDim2.new(1, 0, 1, 0)
    overlay.Position = UDim2.new(0, 0, 0, 0)
    overlay.ZIndex = 2
    overlay.BackgroundTransparency = 0.3
    
    local overlayGradient = Instance.new("UIGradient")
    overlayGradient.Parent = overlay
    overlayGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(20, 20, 20)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0, 0, 0)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(25, 25, 25))
    })
    overlayGradient.Rotation = -45
    
    local animateOverlay = TweenService:Create(
        overlayGradient,
        TweenInfo.new(6, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Rotation = 315}
    )
    animateOverlay:Play()
    table.insert(AnimationConnections, animateOverlay)
    
    return bgFrame
end

local function CreatePulsingCircles(parent)
    for i = 1, 5 do
        local circle = Instance.new("Frame")
        circle.Name = "PulsingCircle" .. i
        circle.Parent = parent
        circle.BackgroundColor3 = Color3.fromRGB(128,86,255)
        circle.BorderSizePixel = 0
        circle.Size = UDim2.new(0, 100 + (i * 50), 0, 100 + (i * 50))
        circle.Position = UDim2.new(0.5, -(50 + (i * 25)), 0.5, -(50 + (i * 25)))
        circle.ZIndex = 3
        circle.BackgroundTransparency = 0.9 + (i * 0.01)
        
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(1, 0)
        corner.Parent = circle
        
        local pulseTween = TweenService:Create(
            circle,
            TweenInfo.new(2 + (i * 0.3), Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
            {
                Size = UDim2.new(0, 120 + (i * 60), 0, 120 + (i * 60)),
                Position = UDim2.new(0.5, -(60 + (i * 30)), 0.5, -(60 + (i * 30))),
                BackgroundTransparency = 0.97
            }
        )
        pulseTween:Play()
        table.insert(AnimationConnections, pulseTween)
    end
end

local function CreateLoadingScreen()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "PremiumLoadingScreen"
    ScreenGui.Parent = PlayerGui
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.ScreenInsets = Enum.ScreenInsets.None
    ScreenGui.IgnoreGuiInset = true
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Parent = ScreenGui
    MainFrame.BackgroundColor3 = LoadingConfig.BackgroundColor
    MainFrame.BorderSizePixel = 0
    MainFrame.Size = UDim2.new(1, 0, 1, 0)
    MainFrame.Position = UDim2.new(0, 0, 0, 0)
    MainFrame.ZIndex = 10
    
    local advancedBg = CreateAdvancedBackground(MainFrame)
    CreatePulsingCircles(MainFrame)
    CreateParticleSystem(MainFrame)
    
    local CenterContainer = Instance.new("Frame")
    CenterContainer.Name = "CenterContainer"
    CenterContainer.Parent = MainFrame
    CenterContainer.BackgroundTransparency = 1
    CenterContainer.Size = UDim2.new(0, 600, 0, 400)
    CenterContainer.Position = UDim2.new(0.5, -300, 0.5, -200)
    CenterContainer.ZIndex = 11
    
    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Name = "TitleLabel"
    TitleLabel.Parent = CenterContainer
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Size = UDim2.new(1, 0, 0, 80)
    TitleLabel.Position = UDim2.new(0, 0, 0, 0)
    TitleLabel.Text = "SAGAZx HUB"
    TitleLabel.TextColor3 = Color3.fromRGB(128,86,255)
    TitleLabel.TextScaled = true
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.ZIndex = 12
    
    local titleStroke = Instance.new("UIStroke")
    titleStroke.Parent = TitleLabel
    titleStroke.Color = Color3.fromRGB(200, 200, 200)
    titleStroke.Thickness = 2
    titleStroke.Transparency = 0.5
    
    local TitleGradient = Instance.new("UIGradient")
    TitleGradient.Parent = TitleLabel
    TitleGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(180, 180, 180)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(180, 180, 180))
    })
    
    local titleAnimation = TweenService:Create(
        TitleGradient,
        TweenInfo.new(3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Offset = Vector2.new(2, 0)}
    )
    titleAnimation:Play()
    table.insert(AnimationConnections, titleAnimation)
    
    local glowTween = TweenService:Create(
        titleStroke,
        TweenInfo.new(2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Transparency = 0.1}
    )
    glowTween:Play()
    table.insert(AnimationConnections, glowTween)
    
    local SubtitleLabel = Instance.new("TextLabel")
    SubtitleLabel.Name = "SubtitleLabel"
    SubtitleLabel.Parent = CenterContainer
    SubtitleLabel.BackgroundTransparency = 1
    SubtitleLabel.Size = UDim2.new(1, 0, 0, 40)
    SubtitleLabel.Position = UDim2.new(0, 0, 0, 90)
    SubtitleLabel.Text = "by SAGAZx"
    SubtitleLabel.TextColor3 = Color3.fromRGB(128,86,255)
    SubtitleLabel.TextScaled = true
    SubtitleLabel.Font = Enum.Font.Gotham
    SubtitleLabel.ZIndex = 12
    
    local ProgressContainer = Instance.new("Frame")
    ProgressContainer.Name = "ProgressContainer"
    ProgressContainer.Parent = CenterContainer
    ProgressContainer.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    ProgressContainer.BorderSizePixel = 0
    ProgressContainer.Size = UDim2.new(1, 0, 0, 25)
    ProgressContainer.Position = UDim2.new(0, 0, 0, 160)
    ProgressContainer.ZIndex = 12
    
    local ProgressCorner = Instance.new("UICorner")
    ProgressCorner.CornerRadius = UDim.new(0, 12)
    ProgressCorner.Parent = ProgressContainer
    
    local ProgressStroke = Instance.new("UIStroke")
    ProgressStroke.Parent = ProgressContainer
    ProgressStroke.Color = Color3.fromRGB(200,200,200)
    ProgressStroke.Thickness = 2
    ProgressStroke.Transparency = 0.3
    
    local strokePulse = TweenService:Create(
        ProgressStroke,
        TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Transparency = 0.1}
    )
    strokePulse:Play()
    table.insert(AnimationConnections, strokePulse)
    
    local ProgressBar = Instance.new("Frame")
    ProgressBar.Name = "ProgressBar"
    ProgressBar.Parent = ProgressContainer
    ProgressBar.BackgroundColor3 = Color3.fromRGB(128,86,255)
    ProgressBar.BorderSizePixel = 0
    ProgressBar.Size = UDim2.new(0, 0, 1, 0)
    ProgressBar.Position = UDim2.new(0, 0, 0, 0)
    ProgressBar.ZIndex = 13
    
    local BarCorner = Instance.new("UICorner")
    BarCorner.CornerRadius = UDim.new(0, 12)
    BarCorner.Parent = ProgressBar
    
    local BarGradient = Instance.new("UIGradient")
    BarGradient.Parent = ProgressBar
    BarGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(180, 180, 180)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(255, 255, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(180, 180, 180))
    })
    
    local barGradientAnim = TweenService:Create(
        BarGradient,
        TweenInfo.new(2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Offset = Vector2.new(1, 0)}
    )
    barGradientAnim:Play()
    table.insert(AnimationConnections, barGradientAnim)
    
    local GlowEffect = Instance.new("Frame")
    GlowEffect.Name = "GlowEffect"
    GlowEffect.Parent = ProgressBar
    GlowEffect.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    GlowEffect.BackgroundTransparency = 1
    GlowEffect.BorderSizePixel = 0
    GlowEffect.Size = UDim2.new(0, 60, 1, 0)
    GlowEffect.Position = UDim2.new(-0.2, 0, 0, 0)
    GlowEffect.ZIndex = 14
    
    local GlowCorner = Instance.new("UICorner")
    GlowCorner.CornerRadius = UDim.new(0, 12)
    GlowCorner.Parent = GlowEffect
    
    local PercentageLabel = Instance.new("TextLabel")
    PercentageLabel.Name = "PercentageLabel"
    PercentageLabel.Parent = CenterContainer
    PercentageLabel.BackgroundTransparency = 1
    PercentageLabel.Size = UDim2.new(1, 0, 0, 40)
    PercentageLabel.Position = UDim2.new(0, 0, 0, 200)
    PercentageLabel.Text = "0%"
    PercentageLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    PercentageLabel.TextScaled = true
    PercentageLabel.Font = Enum.Font.GothamBold
    PercentageLabel.ZIndex = 12
    
    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "StatusLabel"
    StatusLabel.Parent = CenterContainer
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Size = UDim2.new(1, 0, 0, 30)
    StatusLabel.Position = UDim2.new(0, 0, 0, 250)
    StatusLabel.Text = "Preparando..."
    StatusLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
    StatusLabel.TextScaled = true
    StatusLabel.Font = Enum.Font.Gotham
    StatusLabel.ZIndex = 12
    
    local DotsLabel = Instance.new("TextLabel")
    DotsLabel.Name = "DotsLabel"
    DotsLabel.Parent = CenterContainer
    DotsLabel.BackgroundTransparency = 1
    DotsLabel.Size = UDim2.new(0, 60, 0, 30)
    DotsLabel.Position = UDim2.new(1, -60, 0, 250)
    DotsLabel.Text = ""
    DotsLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
    DotsLabel.TextScaled = true
    DotsLabel.Font = Enum.Font.Gotham
    DotsLabel.ZIndex = 12
    
    return ScreenGui, ProgressBar, PercentageLabel, StatusLabel, DotsLabel, GlowEffect, MainFrame
end

local function AnimateDots(dotsLabel)
    local dots = ""
    local dotCount = 0
    
    spawn(function()
        while dotsLabel.Parent do
            dotCount = (dotCount + 1) % 4
            dots = string.rep(".", dotCount)
            dotsLabel.Text = dots
            wait(0.4)
        end
    end)
end

local function StartLoadingSequence()
    CreateLoadingMusic()
    
    local screenGui, progressBar, percentageLabel, statusLabel, dotsLabel, glowEffect, mainFrame = CreateLoadingScreen()
    
    AnimateDots(dotsLabel)
    
    local particleConnection = RunService.Heartbeat:Connect(function()
        UpdateParticles(mainFrame)
    end)
    table.insert(AnimationConnections, particleConnection)
    
    local glowTween = TweenService:Create(
        glowEffect,
        TweenInfo.new(1.2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true),
        {Position = UDim2.new(1.3, 0, 0, 0)}
    )
    glowTween:Play()
    table.insert(AnimationConnections, glowTween)
    
    local startTime = tick()
    local currentElementIndex = 1
    local lastElementTime = startTime
    
    local function updateProgress()
        local elapsed = tick() - startTime
        local progress = math.min(elapsed / LoadingConfig.Duration, 1)
        local percentage = math.floor(progress * 100)
        
        local barTween = TweenService:Create(
            progressBar,
            TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.Out),
            {Size = UDim2.new(progress, 0, 1, 0)}
        )
        barTween:Play()
        
        local percentTween = TweenService:Create(
            percentageLabel,
            TweenInfo.new(0.1, Enum.EasingStyle.Quart, Enum.EasingDirection.Out),
            {TextColor3 = Color3.fromRGB(255, 255, 255)}
        )
        percentTween:Play()
        
        percentageLabel.Text = percentage .. "%"
        
        local elementTime = LoadingConfig.Duration / #LoadingElements
        if tick() - lastElementTime >= elementTime and currentElementIndex <= #LoadingElements then
            statusLabel.Text = LoadingElements[currentElementIndex]
            
            local statusTween = TweenService:Create(
                statusLabel,
                TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
                {TextTransparency = 0}
            )
            statusTween:Play()
            
            currentElementIndex = currentElementIndex + 1
            lastElementTime = tick()
        end
        
        if progress >= 1 then
            wait(0.8)
            
            FadeOutMusic(LoadingConfig.FadeOutTime)
            
            for _, connection in pairs(AnimationConnections) do
                if typeof(connection) == "RBXScriptConnection" then
                    connection:Disconnect()
                elseif typeof(connection) == "Tween" then
                    connection:Cancel()
                end
            end
            
            for _, particle in pairs(Particles) do
                if particle.frame then
                    particle.frame:Destroy()
                end
            end
            
            local fadeOutTween = TweenService:Create(
                mainFrame,
                TweenInfo.new(LoadingConfig.FadeOutTime, Enum.EasingStyle.Quart, Enum.EasingDirection.Out),
                {
                    BackgroundTransparency = 1,
                    Size = UDim2.new(1.5, 0, 1.5, 0),
                    Position = UDim2.new(-0.25, 0, -0.25, 0)
                }
            )
            
            local contentFadeTween = TweenService:Create(
                mainFrame.CenterContainer,
                TweenInfo.new(LoadingConfig.FadeOutTime * 0.7, Enum.EasingStyle.Back, Enum.EasingDirection.In),
                {
                    Size = UDim2.new(0, 0, 0, 0),
                    Position = UDim2.new(0.5, 0, 0.5, 0)
                }
            )
            
            fadeOutTween:Play()
            contentFadeTween:Play()
            
            fadeOutTween.Completed:Connect(function()
                screenGui:Destroy()
            end)
            
            return
        end
        
        RunService.Heartbeat:Wait()
        updateProgress()
    end
    updateProgress()
end

StartLoadingSequence()

wait(5)

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