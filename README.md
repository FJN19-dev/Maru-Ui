-- Slayer UI Library
-- by Domain Slayer 😈

local SlayerUI = {}
SlayerUI.__index = SlayerUI

-- Serviços
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- =========================
-- Create Window
-- =========================
function SlayerUI:CreateWindow(config)
    local Window = {}
    Window.Tabs = {}

    -- ScreenGui
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "SlayerUI"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = PlayerGui

    -- Main Frame
    local Main = Instance.new("Frame")
    Main.Size = UDim2.fromOffset(520, 360)
    Main.Position = UDim2.fromScale(0.5, 0.5)
    Main.AnchorPoint = Vector2.new(0.5, 0.5)
    Main.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    Main.BorderSizePixel = 0
    Main.Parent = ScreenGui

    local Corner = Instance.new("UICorner", Main)
    Corner.CornerRadius = UDim.new(0, 12)

    -- Title
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.BackgroundTransparency = 1
    Title.Text = config.Title or "Slayer UI"
    Title.TextColor3 = Color3.fromRGB(255, 0, 0)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 18
    Title.Parent = Main

    -- Tabs Holder
    local TabsHolder = Instance.new("Frame")
    TabsHolder.Size = UDim2.fromOffset(120, 300)
    TabsHolder.Position = UDim2.fromOffset(10, 50)
    TabsHolder.BackgroundTransparency = 1
    TabsHolder.Parent = Main

    -- Content Holder
    local ContentHolder = Instance.new("Frame")
    ContentHolder.Size = UDim2.fromOffset(360, 300)
    ContentHolder.Position = UDim2.fromOffset(150, 50)
    ContentHolder.BackgroundTransparency = 1
    ContentHolder.Parent = Main

    -- =========================
    -- Add Tab
    -- =========================
    function Window:AddTab(name)
        local Tab = {}
        
        local Button = Instance.new("TextButton")
        Button.Size = UDim2.fromOffset(120, 35)
        Button.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        Button.Text = name
        Button.TextColor3 = Color3.fromRGB(200, 200, 200)
        Button.Font = Enum.Font.Gotham
        Button.TextSize = 14
        Button.Parent = TabsHolder

        local BtnCorner = Instance.new("UICorner", Button)
        BtnCorner.CornerRadius = UDim.new(0, 8)

        local Page = Instance.new("Frame")
        Page.Size = UDim2.fromScale(1, 1)
        Page.BackgroundTransparency = 1
        Page.Visible = false
        Page.Parent = ContentHolder

        Button.MouseButton1Click:Connect(function()
            for _, t in pairs(ContentHolder:GetChildren()) do
                if t:IsA("Frame") then
                    t.Visible = false
                end
            end
            Page.Visible = true
        end)

        -- =========================
        -- Add Button
        -- =========================
        function Tab:AddButton(text, callback)
            local Btn = Instance.new("TextButton")
            Btn.Size = UDim2.new(1, -10, 0, 35)
            Btn.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
            Btn.Text = text
            Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
            Btn.Font = Enum.Font.Gotham
            Btn.TextSize = 14
            Btn.Parent = Page

            local Corner = Instance.new("UICorner", Btn)
            Corner.CornerRadius = UDim.new(0, 8)

            Btn.MouseButton1Click:Connect(function()
                pcall(callback)
            end)
        end

        table.insert(Window.Tabs, Tab)
        return Tab
    end

    return Window
end

return setmetatable({}, SlayerUI)
