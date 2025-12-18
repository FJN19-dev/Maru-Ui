-- Slayer UI Library
-- by Domain Slayer 😈

local SlayerUI = {}
SlayerUI.__index = SlayerUI

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- =========================
-- Drag
-- =========================
local function MakeDraggable(frame)
    local dragging, dragInput, startPos, startFramePos

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            startPos = input.Position
            startFramePos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - startPos
            frame.Position = UDim2.new(
                startFramePos.X.Scale,
                startFramePos.X.Offset + delta.X,
                startFramePos.Y.Scale,
                startFramePos.Y.Offset + delta.Y
            )
        end
    end)
end

-- =========================
-- Create Window
-- =========================
function SlayerUI:CreateWindow(config)
    local Window = {}
    Window.Tabs = {}
    Window.TabCount = 0
    Window.MaxTabs = 20

    -- ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "SlayerUI"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = PlayerGui

    -- Main
    local Main = Instance.new("Frame")
    Main.Size = UDim2.fromOffset(520, 360)
    Main.Position = UDim2.fromScale(0.5, 0.5)
    Main.AnchorPoint = Vector2.new(0.5, 0.5)
    Main.BackgroundColor3 = Color3.fromRGB(20,20,20)
    Main.BorderSizePixel = 0
    Main.Parent = ScreenGui
    MakeDraggable(Main)

    Instance.new("UICorner", Main).CornerRadius = UDim.new(0,12)

    -- Title Bar
    local Top = Instance.new("Frame")
    Top.Size = UDim2.new(1,0,0,40)
    Top.BackgroundTransparency = 1
    Top.Parent = Main

    -- Title
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1,-100,1,0)
    Title.Position = UDim2.fromOffset(10,0)
    Title.BackgroundTransparency = 1
    Title.Text = config.Title or "Slayer UI"
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18
    Title.TextColor3 = Color3.fromRGB(255,0,0)
    Title.TextXAlignment = Left
    Title.Parent = Top

    -- Minimize
    local Min = Instance.new("TextButton")
    Min.Size = UDim2.fromOffset(30,30)
    Min.Position = UDim2.fromOffset(460,5)
    Min.Text = "-"
    Min.Font = Enum.Font.GothamBold
    Min.TextSize = 20
    Min.BackgroundColor3 = Color3.fromRGB(35,35,35)
    Min.TextColor3 = Color3.new(1,1,1)
    Min.Parent = Top
    Instance.new("UICorner", Min).CornerRadius = UDim.new(0,8)

    -- Close
    local Close = Instance.new("TextButton")
    Close.Size = UDim2.fromOffset(30,30)
    Close.Position = UDim2.fromOffset(495,5)
    Close.Text = "X"
    Close.Font = Enum.Font.GothamBold
    Close.TextSize = 14
    Close.BackgroundColor3 = Color3.fromRGB(120,0,0)
    Close.TextColor3 = Color3.new(1,1,1)
    Close.Parent = Top
    Instance.new("UICorner", Close).CornerRadius = UDim.new(0,8)

    -- Icon
    local Icon = Instance.new("TextButton")
    Icon.Size = UDim2.fromOffset(45,45)
    Icon.Position = UDim2.fromScale(0.1,0.5)
    Icon.Text = "😈"
    Icon.Font = Enum.Font.GothamBold
    Icon.TextSize = 20
    Icon.BackgroundColor3 = Color3.fromRGB(25,25,25)
    Icon.TextColor3 = Color3.fromRGB(255,0,0)
    Icon.Visible = false
    Icon.Parent = ScreenGui
    Instance.new("UICorner", Icon).CornerRadius = UDim.new(1,0)
    MakeDraggable(Icon)

    -- Containers
    local TabsHolder = Instance.new("Frame")
    TabsHolder.Size = UDim2.fromOffset(120,300)
    TabsHolder.Position = UDim2.fromOffset(10,50)
    TabsHolder.BackgroundTransparency = 1
    TabsHolder.Parent = Main

    local UIList = Instance.new("UIListLayout", TabsHolder)
    UIList.Padding = UDim.new(0,6)

    local Content = Instance.new("Frame")
    Content.Size = UDim2.fromOffset(360,300)
    Content.Position = UDim2.fromOffset(150,50)
    Content.BackgroundTransparency = 1
    Content.Parent = Main

    -- Buttons logic
    Min.MouseButton1Click:Connect(function()
        Main.Visible = false
        Icon.Visible = true
    end)

    Icon.MouseButton1Click:Connect(function()
        Main.Visible = not Main.Visible
    end)

    Close.MouseButton1Click:Connect(function()
        ScreenGui:Destroy()
    end)

    -- =========================
    -- Add Tab
    -- =========================
    function Window:AddTab(name)
        if Window.TabCount >= Window.MaxTabs then
            warn("[Slayer UI] Limite de 20 tabs atingido.")
            return
        end
        Window.TabCount += 1

        local Tab = {}

        local TabBtn = Instance.new("TextButton")
        TabBtn.Size = UDim2.fromOffset(120,35)
        TabBtn.BackgroundColor3 = Color3.fromRGB(30,30,30)
        TabBtn.Text = name
        TabBtn.Font = Enum.Font.Gotham
        TabBtn.TextSize = 14
        TabBtn.TextColor3 = Color3.fromRGB(200,200,200)
        TabBtn.Parent = TabsHolder
        Instance.new("UICorner", TabBtn).CornerRadius = UDim.new(0,8)

        local Page = Instance.new("Frame")
        Page.Size = UDim2.fromScale(1,1)
        Page.BackgroundTransparency = 1
        Page.Visible = false
        Page.Parent = Content

        local List = Instance.new("UIListLayout", Page)
        List.Padding = UDim.new(0,8)

        TabBtn.MouseButton1Click:Connect(function()
            for _,v in pairs(Content:GetChildren()) do
                if v:IsA("Frame") then v.Visible = false end
            end
            Page.Visible = true
        end)
        
        function Tab:AddToggle(text, default, callback)
    local toggled = default or false

    local Holder = Instance.new("Frame")
    Holder.Size = UDim2.new(1,-10,0,35)
    Holder.BackgroundColor3 = Color3.fromRGB(35,35,35)
    Holder.Parent = Page
    Instance.new("UICorner", Holder).CornerRadius = UDim.new(0,8)

    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1,-50,1,0)
    Label.Position = UDim2.fromOffset(10,0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 14
    Label.TextColor3 = Color3.new(1,1,1)
    Label.TextXAlignment = Left
    Label.Parent = Holder

    local ToggleBtn = Instance.new("TextButton")
    ToggleBtn.Size = UDim2.fromOffset(30,20)
    ToggleBtn.Position = UDim2.fromOffset(Holder.AbsoluteSize.X-40,7)
    ToggleBtn.AnchorPoint = Vector2.new(1,0)
    ToggleBtn.BackgroundColor3 = toggled and Color3.fromRGB(0,170,0) or Color3.fromRGB(80,80,80)
    ToggleBtn.Text = ""
    ToggleBtn.Parent = Holder
    Instance.new("UICorner", ToggleBtn).CornerRadius = UDim.new(1,0)

    ToggleBtn.MouseButton1Click:Connect(function()
        toggled = not toggled
        ToggleBtn.BackgroundColor3 = toggled and Color3.fromRGB(0,170,0) or Color3.fromRGB(80,80,80)
        pcall(callback, toggled)
    end)

    pcall(callback, toggled)
end

       function Tab:AddDropdown(text, options, callback)
    local opened = false

    local Holder = Instance.new("Frame")
    Holder.Size = UDim2.new(1,-10,0,35)
    Holder.BackgroundColor3 = Color3.fromRGB(35,35,35)
    Holder.ClipsDescendants = true
    Holder.Parent = Page
    Instance.new("UICorner", Holder).CornerRadius = UDim.new(0,8)

    local Label = Instance.new("TextButton")
    Label.Size = UDim2.new(1,-10,0,35)
    Label.Position = UDim2.fromOffset(5,0)
    Label.BackgroundTransparency = 1
    Label.Text = text
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 14
    Label.TextColor3 = Color3.new(1,1,1)
    Label.TextXAlignment = Left
    Label.Parent = Holder

    local List = Instance.new("Frame")
    List.Size = UDim2.new(1,0,0,#options*30)
    List.Position = UDim2.fromOffset(0,35)
    List.BackgroundTransparency = 1
    List.Parent = Holder

    local UIList = Instance.new("UIListLayout", List)
    UIList.Padding = UDim.new(0,5)

    for _,opt in pairs(options) do
        local Btn = Instance.new("TextButton")
        Btn.Size = UDim2.new(1,-10,0,25)
        Btn.Position = UDim2.fromOffset(5,0)
        Btn.BackgroundColor3 = Color3.fromRGB(45,45,45)
        Btn.Text = opt
        Btn.Font = Enum.Font.Gotham
        Btn.TextSize = 13
        Btn.TextColor3 = Color3.new(1,1,1)
        Btn.Parent = List
        Instance.new("UICorner", Btn).CornerRadius = UDim.new(0,6)

        Btn.MouseButton1Click:Connect(function()
            Label.Text = text..": "..opt
            opened = false
            Holder.Size = UDim2.new(1,-10,0,35)
            pcall(callback, opt)
        end)
    end

    Label.MouseButton1Click:Connect(function()
        opened = not opened
        Holder.Size = opened and UDim2.new(1,-10,0,35 + (#options*30)) or UDim2.new(1,-10,0,35)
    end)
end

       function Tab:AddParagraph(title, description)
    local Holder = Instance.new("Frame")
    Holder.Size = UDim2.new(1,-10,0,60)
    Holder.BackgroundColor3 = Color3.fromRGB(30,30,30)
    Holder.Parent = Page
    Instance.new("UICorner", Holder).CornerRadius = UDim.new(0,8)

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1,-10,0,20)
    Title.Position = UDim2.fromOffset(5,5)
    Title.BackgroundTransparency = 1
    Title.Text = title
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 14
    Title.TextColor3 = Color3.fromRGB(255,0,0)
    Title.TextXAlignment = Left
    Title.Parent = Holder

    local Desc = Instance.new("TextLabel")
    Desc.Size = UDim2.new(1,-10,0,30)
    Desc.Position = UDim2.fromOffset(5,25)
    Desc.BackgroundTransparency = 1
    Desc.TextWrapped = true
    Desc.TextYAlignment = Top
    Desc.Text = description
    Desc.Font = Enum.Font.Gotham
    Desc.TextSize = 13
    Desc.TextColor3 = Color3.fromRGB(200,200,200)
    Desc.TextXAlignment = Left
    Desc.Parent = Holder
end

     

        -- Button
        function Tab:AddButton(text, callback)
            local Btn = Instance.new("TextButton")
            Btn.Size = UDim2.new(1,-10,0,35)
            Btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
            Btn.Text = text
            Btn.Font = Enum.Font.Gotham
            Btn.TextSize = 14
            Btn.TextColor3 = Color3.new(1,1,1)
            Btn.Parent = Page
            Instance.new("UICorner", Btn).CornerRadius = UDim.new(0,8)

            Btn.MouseButton1Click:Connect(function()
                pcall(callback)
            end)
        end

        return Tab
    end

    return Window
end

return setmetatable({}, SlayerUI)
