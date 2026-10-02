-- COOLGUI RED/BLACK EDITION v3.0
-- สคริปต์แกล้งคน + ระบบแอดมินปลอม + กันแบน

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local camera = workspace.CurrentCamera

-- ระบบกันแบน (Anti-Ban)
local function AntiBan()
    local mt = getrawmetatable(game)
    local oldNamecall = mt.__namecall
    setreadonly(mt, false)
    
    mt.__namecall = newcclosure(function(...)
        local args = {...}
        local method = getnamecallmethod()
        
        if method == "Kick" or method == "Ban" then
            return "Blocked"
        end
        
        return oldNamecall(...)
    end)
    
    setreadonly(mt, true)
end
AntiBan()

-- สร้าง GUI หลัก
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CoolGUI_RedBlack"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- ฟังก์ชันสร้างปุ่ม
local function createButton(parent, text, pos, size, color, callback)
    local btn = Instance.new("TextButton")
    btn.Size = size or UDim2.new(0, 260, 0, 30)
    btn.Position = pos
    btn.BackgroundColor3 = color
    btn.Text = text
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextSize = 14
    btn.Font = Enum.Font.GothamBold
    btn.Parent = parent
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- สร้างหน้าต่างหลัก
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 350, 0, 550)
mainFrame.Position = UDim2.new(0.5, -175, 0.5, -275)
mainFrame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
mainFrame.BackgroundTransparency = 0.05
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Parent = screenGui

-- หัวข้อ GUI
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 35)
titleBar.BackgroundColor3 = Color3.new(0.8, 0, 0)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -40, 1, 0)
title.BackgroundTransparency = 1
title.Text = "🔥 COOLGUI RED/BLACK v3.0"
title.TextColor3 = Color3.new(1, 1, 1)
title.TextSize = 16
title.Font = Enum.Font.GothamBlack
title.Parent = titleBar

-- ปุ่มเปิด/ปิด GUI
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 30, 0, 30)
toggleBtn.Position = UDim2.new(1, -35, 0, 2.5)
toggleBtn.BackgroundColor3 = Color3.new(0.5, 0, 0)
toggleBtn.Text = "✕"
toggleBtn.TextColor3 = Color3.new(1, 1, 1)
toggleBtn.TextSize = 14
toggleBtn.Parent = titleBar

local guiOpen = true
toggleBtn.MouseButton1Click:Connect(function()
    guiOpen = not guiOpen
    mainFrame.Visible = guiOpen
    if guiOpen then
        toggleBtn.Text = "✕"
    else
        toggleBtn.Text = "☰"
    end
end)

-- ปุ่มลาก GUI
local dragging = false
local dragStart, startPos

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

titleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

screenGui.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- ปุ่มปรับขนาด GUI
local resizeBtn = Instance.new("TextButton")
resizeBtn.Size = UDim2.new(0, 20, 0, 20)
resizeBtn.Position = UDim2.new(1, -25, 1, -25)
resizeBtn.BackgroundColor3 = Color3.new(0.8, 0, 0)
resizeBtn.Text = "⤡"
resizeBtn.TextColor3 = Color3.new(1, 1, 1)
resizeBtn.TextSize = 12
resizeBtn.Parent = mainFrame

local resizing = false
resizeBtn.MouseButton1Down:Connect(function()
    resizing = true
end)

mouse.MouseButton1Up:Connect(function()
    resizing = false
end)

mouse.MouseMoved:Connect(function()
    if resizing then
        local x = math.clamp(mouse.X - mainFrame.AbsolutePosition.X, 200, 600)
        local y = math.clamp(mouse.Y - mainFrame.AbsolutePosition.Y, 300, 800)
        mainFrame.Size = UDim2.new(0, x, 0, y)
    end
end)

-- สร้าง ScrollFrame สำหรับเนื้อหา
local scrollFrame = Instance.new("ScrollingFrame")
scrollFrame.Size = UDim2.new(1, 0, 1, -35)
scrollFrame.Position = UDim2.new(0, 0, 0, 35)
scrollFrame.BackgroundTransparency = 1
scrollFrame.ScrollBarThickness = 5
scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 800)
scrollFrame.Parent = mainFrame

-- ฟังก์ชันสร้างปุ่มใน ScrollFrame
local function addButton(text, color, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 310, 0, 35)
    btn.Position = UDim2.new(0, 20, 0, #scrollFrame:GetChildren() * 45)
    btn.BackgroundColor3 = color
    btn.Text = text
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.TextSize = 14
    btn.Font = Enum.Font.GothamBold
    btn.Parent = scrollFrame
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- 1. ปรับความเร็วและสปีด
addButton("⚡ ปรับความเร็วเดิน", Color3.new(0.8, 0, 0), function()
    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if hrp and humanoid then
        humanoid.WalkSpeed = 100
        humanoid.JumpPower = 100
    end
end)

-- 2. โหมดบิน + ปรับความเร็วบิน
local flyEnabled = false
local flySpeed = 100
addButton("🕊️ โหมดบิน ON/OFF", Color3.new(0.6, 0, 0), function()
    flyEnabled = not flyEnabled
    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        if flyEnabled then
            local bv = Instance.new("BodyVelocity")
            bv.Name = "FlyVelocity"
            bv.MaxForce = Vector3.new(1, 1, 1) * 100000
            bv.Velocity = Vector3.new(0, 0, 0)
            bv.Parent = hrp
            
            local bg = Instance.new("BodyGyro")
            bg.Name = "FlyGyro"
            bg.MaxTorque = Vector3.new(1, 1, 1) * 100000
            bg.CFrame = hrp.CFrame
            bg.Parent = hrp
            
            mouse.KeyDown:Connect(function(key)
                if flyEnabled then
                    if key == "w" then
                        bv.Velocity = hrp.CFrame.LookVector * flySpeed
                    elseif key == "s" then
                        bv.Velocity = -hrp.CFrame.LookVector * flySpeed
                    elseif key == "a" then
                        bv.Velocity = -hrp.CFrame.RightVector * flySpeed
                    elseif key == "d" then
                        bv.Velocity = hrp.CFrame.RightVector * flySpeed
                    elseif key == "space" then
                        bv.Velocity = Vector3.new(0, flySpeed, 0)
                    elseif key == "q" then
                        bv.Velocity = Vector3.new(0, -flySpeed, 0)
                    end
                end
            end)
        else
            local bv = hrp:FindFirstChild("FlyVelocity")
            local bg = hrp:FindFirstChild("FlyGyro")
            if bv then bv:Destroy() end
            if bg then bg:Destroy() end
        end
    end
end)

-- ปรับความเร็วบิน
addButton("✈️ ความเร็วบิน 200", Color3.new(0.5, 0, 0), function()
    flySpeed = 200
end)

addButton("✈️ ความเร็วบิน 500", Color3.new(0.5, 0, 0), function()
    flySpeed = 500
end)

-- 3. ปรับความสูงกระโดด
addButton("🦘 กระโดดสูง 200", Color3.new(0.7, 0, 0), function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.JumpPower = 200
    end
end)

addButton("🦘 กระโดดสูง 500", Color3.new(0.7, 0, 0), function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.JumpPower = 500
    end
end)

-- 4. เตะคนให้กระเด็น (ไม่เตะออกจากเซิร์ฟเวอร์)
addButton("👊 เตะคนกระเด็น (คลิกที่คน)", Color3.new(1, 0, 0), function()
    mouse.Button1Down:Connect(function()
        local target = mouse.Target
        if target then
            local character = target:FindFirstAncestorOfClass("Model")
            if character and character ~= player.Character then
                local hrp = character:FindFirstChild("HumanoidRootPart")
                local humanoid = character:FindFirstChildOfClass("Humanoid")
                if hrp and humanoid then
                    -- ทำให้กระเด็นแบบแรงๆ
                    local bv = Instance.new("BodyVelocity")
                    bv.MaxForce = Vector3.new(1, 1, 1) * 100000
                    bv.Velocity = (hrp.Position - player.Character.HumanoidRootPart.Position).Unit * 500 + Vector3.new(0, 200, 0)
                    bv.Parent = hrp
                    
                    task.wait(0.1)
                    bv:Destroy()
                    
                    -- สลบชั่วคราว
                    humanoid:ChangeState(Enum.HumanoidStateType.Physics)
                    task.wait(1)
                    humanoid:ChangeState(Enum.HumanoidStateType.Running)
                end
            end
        end
    end)
end)

-- 5. ระบบเปลี่ยนแปลงแผนที่ (ทุกคนมองเห็น)
local mapEnabled = false
addButton("🗺️ โหมดเปลี่ยนแผนที่", Color3.new(0.8, 0, 0), function()
    mapEnabled = not mapEnabled
end)

-- เปลี่ยนสีท้องฟ้า
addButton("🌈 เปลี่ยนสีท้องฟ้า", Color3.new(0.6, 0, 0), function()
    if mapEnabled then
        local lighting = game:GetService("Lighting")
        lighting.Ambient = Color3.new(math.random(), math.random(), math.random())
        lighting.OutdoorAmbient = Color3.new(math.random(), math.random(), math.random())
    end
end)

-- ย้ายผู้เล่นทั้งหมดไปที่จุดสุ่ม
addButton("🌀 ย้ายทุกคนไปสุ่ม", Color3.new(0.6, 0, 0), function()
    if mapEnabled then
        for _, v in ipairs(game.Players:GetPlayers()) do
            local hrp = v.Character and v.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.CFrame = CFrame.new(math.random(-500, 500), 100, math.random(-500, 500))
            end
        end
    end
end)

-- ลบพื้นทั้งหมด (ทุกคนตก)
addButton("💥 ลบพื้นทั้งหมด", Color3.new(1, 0, 0), function()
    if mapEnabled then
        for _, v in ipairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and v.Name ~= "Baseplate" and not v:IsDescendantOf(player.Character) then
                v.Anchored = false
                v:Destroy()
            end
        end
    end
end)

-- สร้างกำแพงกั้นทุกคน
addButton("🧱 สร้างกำแพงรอบตัว", Color3.new(0.5, 0, 0), function()
    if mapEnabled then
        local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            for i = 1, 20 do
                local wall = Instance.new("Part")
                wall.Size = Vector3.new(10, 100, 10)
                wall.Position = hrp.Position + Vector3.new(math.cos(i * 18) * 20, 50, math.sin(i * 18) * 20)
                wall.Anchored = true
                wall.BrickColor = BrickColor.new("Really red")
                wall.Material = Enum.Material.Neon
                wall.Parent = workspace
            end
        end
    end
end)

-- 6. ฟังก์ชันแอดมินปลอม
addButton("👑 โหมดแอดมินปลอม", Color3.new(0.9, 0.8, 0), function()
    -- แสดงข้อความแอดมิน
    local hint = Instance.new("Hint")
    hint.Text = "⚠️ " .. player.Name .. " ได้รับสิทธิ์แอดมิน! ⚠️"
    hint.Parent = workspace
    
    task.wait(3)
    hint:Destroy()
end)

-- สร้างป้ายชื่อแอดมิน
addButton("🏷️ ป้ายชื่อแอดมิน", Color3.new(0.9, 0.8, 0), function()
    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        local billboard = Instance.new("BillboardGui")
        billboard.Size = UDim2.new(0, 200, 0, 50)
        billboard.Adornee = hrp
        billboard.Parent = hrp
        
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.Text = "👑 ADMIN 👑"
        label.TextColor3 = Color3.new(1, 0, 0)
        label.TextSize = 20
        label.Font = Enum.Font.GothamBlack
        label.Parent = billboard
    end
end)

-- 7. ฟังก์ชันแกล้งคนอื่น
-- ทำให้คนอื่นมึนงง
addButton("🌀 ทำให้คนมึนงง", Color3.new(0.8, 0, 0), function()
    for _, v in ipairs(game.Players:GetPlayers()) do
        if v ~= player then
            local hrp = v.Character and v.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local bv = Instance.new("BodyVelocity")
                bv.MaxForce = Vector3.new(1, 1, 1) * 100000
                bv.Velocity = Vector3.new(math.random(-100, 100), math.random(50, 200), math.random(-100, 100))
                bv.Parent = hrp
                task.wait(0.5)
                bv:Destroy()
            end
        end
    end
end)

-- สลับตำแหน่งคน
addButton("🔄 สลับตำแหน่งทุกคน", Color3.new(0.8, 0, 0), function()
    local players = {}
    for _, v in ipairs(game.Players:GetPlayers()) do
        if v ~= player and v.Character then
            table.insert(players, v)
        end
    end
    
    for i = 1, #players, 2 do
        if players[i+1] then
            local hrp1 = players[i].Character:FindFirstChild("HumanoidRootPart")
            local hrp2 = players[i+1].Character:FindFirstChild("HumanoidRootPart")
            if hrp1 and hrp2 then
                local pos1 = hrp1.CFrame
                hrp1.CFrame = hrp2.CFrame
                hrp2.CFrame = pos1
            end
        end
    end
end)

-- ทำให้คนอื่นตาบอดชั่วคราว
addButton("👁️ ทำให้ตาบอด", Color3.new(0.8, 0, 0), function()
    for _, v in ipairs(game.Players:GetPlayers()) do
        if v ~= player then
            local char = v.Character
            if char then
                local blackScreen = Instance.new("Part")
                blackScreen.Size = Vector3.new(500, 500, 500)
                blackScreen.Position = v.Character.HumanoidRootPart.Position
                blackScreen.Anchored = true
                blackScreen.Transparency = 0.5
                blackScreen.BrickColor = BrickColor.new("Black")
                blackScreen.Parent = workspace
                task.wait(3)
                blackScreen:Destroy()
            end
        end
    end
end)

-- 8. ฟังก์ชันพิเศษอื่นๆ
-- ระบบดูดทรัพย์
addButton("💰 ดูดเงินทั้งหมด", Color3.new(0, 0.5, 0), function()
    for _, v in ipairs(game.Players:GetPlayers()) do
        if v ~= player then
            local leaderstats = v:FindFirstChild("leaderstats")
            if leaderstats then
                for _, stat in ipairs(leaderstats:GetChildren()) do
                    if stat.ValueType == "IntValue" then
                        stat.Value = 0
                    end
                end
            end
        end
    end
end)

-- ระบบฟื้นฟู
addButton("❤️ ฟื้นฟูพลัง", Color3.new(0, 0.5, 0), function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.Health = humanoid.MaxHealth
    end
end)

-- ระบบล่องหน
addButton("👻 ล่องหน", Color3.new(0.5, 0, 0.5), function()
    for _, v in ipairs(player.Character:GetChildren()) do
        if v:IsA("BasePart") then
            v.Transparency = 1
        end
    end
end)

-- ระบบกันตก
addButton("🛡️ กันตก", Color3.new(0, 0, 0.8), function()
    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        local bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(0, 1, 0) * 100000
        bv.Velocity = Vector3.new(0, 0, 0)
        bv.Parent = hrp
    end
end)

-- ปุ่มรีเซ็ตทั้งหมด
addButton("🔄 รีเซ็ตทั้งหมด", Color3.new(0.3, 0.3, 0.3), function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.Health = 0
        task.wait(2)
        humanoid.WalkSpeed = 16
        humanoid.JumpPower = 50
        flyEnabled = false
        mapEnabled = false
    end
end)

-- ข้อความเตือน
local warning = Instance.new("TextLabel")
warning.Size = UDim2.new(1, -40, 0, 20)
warning.Position = UDim2.new(0, 20, 1, -25)
warning.BackgroundTransparency = 1
warning.Text = "⚠️ ใช้สคริปต์นี้เสี่ยงโดนแบน!"
warning.TextColor3 = Color3.new(1, 0, 0)
warning.TextSize = 12
warning.Font = Enum.Font.GothamBold
warning.Parent = mainFrame

print("✅ CoolGUI Red/Black Edition v3.0 โหลดสำเร็จ!")
print("🎮 กดปุ่ม ☰ เพื่อเปิด/ปิด GUI")
